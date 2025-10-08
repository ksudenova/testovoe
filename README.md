# testovoe
const serialize = (set) => {
    const bits = new Uint8Array(38);
    set.forEach(num => {
        if (num >= 1 && num <= 300) bits[(num - 1) >> 3] |= 1 << ((num - 1) & 7);
    });
    return Buffer.from(bits).toString('base64');
};

const deserialize = (str) => {
    const bits = new Uint8Array(Buffer.from(str, 'base64'));
    const result = new Set();
    for (let i = 0; i < 300; i++) {
        if (bits[i >> 3] & (1 << (i & 7))) result.add(i + 1);
    }
    return result;
};

const tests = [
    { input: new Set([1, 2, 3]), simple: "1,2,3" },
    { input: new Set([1, 300]), simple: "1,300" },
    { input: new Set([...Array(50)].map(() => Math.floor(Math.random() * 300) + 1)), simple: [...Array(50)].map(() => Math.floor(Math.random() * 300) + 1).join(',') },
    { input: new Set([...Array(100)].map(() => Math.floor(Math.random() * 300) + 1)), simple: [...Array(100)].map(() => Math.floor(Math.random() * 300) + 1).join(',') },
    { input: new Set([...Array(500)].map(() => Math.floor(Math.random() * 300) + 1)), simple: [...Array(500)].map(() => Math.floor(Math.random() * 300) + 1).join(',') },
    { input: new Set([...Array(1000)].map(() => Math.floor(Math.random() * 300) + 1)), simple: [...Array(1000)].map(() => Math.floor(Math.random() * 300) + 1).join(',') },
    { input: new Set([1, 2, 3, 4, 5, 6, 7, 8, 9]), simple: "1,2,3,4,5,6,7,8,9" },
    { input: new Set([10, 20, 30, 40, 50, 60, 70, 80, 90]), simple: "10,20,30,40,50,60,70,80,90" },
    { input: new Set([100, 200, 300]), simple: "100,200,300" },
    { input: new Set([...Array(300).keys()].map(i => i + 1)), simple: [...Array(300).keys()].map(i => i + 1).join(',') },
];

tests.forEach((test, idx) => {
    const serialized = serialize(test.input);
    const deserialized = deserialize(serialized);
    const simpleLen = test.simple.length;
    const compressedLen = serialized.length;
    const compressionRatio = simpleLen / compressedLen;
    console.log(`Test ${idx + 1}:`);
    console.log(`Input: ${[...test.input].join(',')}`);
    console.log(`Simple: ${test.simple} (len: ${simpleLen})`);
    console.log(`Compressed: ${serialized} (len: ${compressedLen})`);
    console.log(`Deserialized: ${[...deserialized].join(',')}`);
    console.log(`Compression Ratio: ${compressionRatio.toFixed(2)}x`);
    console.log(`Match: ${JSON.stringify([...deserialized].sort()) === JSON.stringify([...test.input].sort())}`);
    console.log('---');
});
