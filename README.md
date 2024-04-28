# Blockchain
class Block {
    constructor(index, timestamp, data, previousHash = '') {
        this.index = index;
        this.timestamp = timestamp;
        this.data = data;
        this.previousHash = previousHash;
        this.hash = this.calculateHash();
        this.nonce = 0;
    }

    calculateHash() {
        return CryptoJS.SHA256(this.index + this.timestamp + JSON.stringify(this.data) + this.previousHash + this.nonce).toString();
    }

    mineBlock(difficulty) {
        while (this.hash.substring(0, difficulty) !== Array(difficulty + 1).join("0")) {
            this.nonce++;
            this.hash = this.calculateHash();
        }
        console.log("Block mined: " + this.hash);
    }
}

class Blockchain {
    constructor() {
        this.chain = [this.createGenesisBlock()];
        this.difficulty = 4; // Dificultad de minado
        this.balanceMinado = 0; // Balance total de AguaraCoin minadas
    }

    createGenesisBlock() {
        return new Block(0, "01/01/2020", "Genesis block", "0");
    }

    getLatestBlock() {
        return this.chain[this.chain.length - 1];
    }

    mineNewBlock(data) {
        const newBlock = new Block(this.chain.length, new Date().toLocaleString(), data);
        newBlock.previousHash = this.getLatestBlock().hash;
        newBlock.mineBlock(this.difficulty);
        this.chain.push(newBlock);
        this.displayChain(newBlock);
        this.balanceMinado++; // Incrementar el balance de AguaraCoin minadas
        document.getElementById('balanceMinado').innerText = this.balanceMinado + ' AguaraCoin (AGUARA)';
        continueMining(); // Continuar la minería
    }

    displayChain(block) {
        const minedDataDiv = document.getElementById('minedData');
        const blockDiv = document.createElement('details');
        blockDiv.innerHTML = 
            `<summary>Block ${block.index}</summary>
            <div>Timestamp: ${block.timestamp}</div>
            <div>Data: ${block.data}</div>
            <div>Previous Hash: ${block.previousHash}</div>
            <div>Hash: ${block.hash}</div>`;
        minedDataDiv.appendChild(blockDiv);
    }
}

let miniBlockchain = new Blockchain();
let miningInterval;
