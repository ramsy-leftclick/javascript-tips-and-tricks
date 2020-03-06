```javascript
import axios from 'axios';

const NUMBER_OF_ITEMS = 112;
const MAX_CONCURRENCY = 4;

const urls = [];

for (let i = 10; i <= NUMBER_OF_ITEMS; i++) { urls.push(`http://localhost.localdomain:3002/slides/${i}.json?includes[]=null`);}

const fetchURL = (url) => axios.get(url, {withCredentials: true})
    .then(res => ({id: res.data.id}))
    .catch(err => err);

const splitInBatch = (arr, batchSize) => {
    return arr.reduce((accumulator, element, index) => {
        const batchIndex = Math.floor(index / batchSize);
        if (Array.isArray(accumulator[batchIndex])) {
            accumulator[batchIndex].push(element);
        } else {
            accumulator.push([element]);
        }
        return accumulator;
    }, []);
};

const batches = splitInBatch(urls, MAX_CONCURRENCY);

const c = Math.ceil(100 / batches.length);

const process = async (batches) => {
    const all = [];

    await batches.reduce(async (previousBatch, currentBatch, index) => {
        await previousBatch;
        console.log(`Processing ${c * index}%`);
        const currentBatchPromises = currentBatch.map(url => fetchURL(url));
        const result = await Promise.all(currentBatchPromises);
        all.push(...result);
    }, Promise.resolve());

    return all;
};

process(batches).then(res => console.log(res));




```
