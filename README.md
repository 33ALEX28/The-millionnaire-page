// Solana Web3.js initialization
const { Connection, PublicKey, SystemProgram, Transaction } = solanaWeb3;
const connection = new Connection('https://api.mainnet-beta.solana.com', 'confirmed');
let wallet = null;

const gridElement = document.getElementById("pixel-grid");
const numRows = 500;  // 500 wierszy
const numCols = 100;  // 100 kolumn

// Dynamically generate the grid
for (let i = 0; i < numRows * numCols; i++) {
    const pixel = document.createElement("div");
    pixel.className = "pixel";
    pixel.id = "pixel-" + i;
    gridElement.appendChild(pixel);

    pixel.onclick = () => {
        if (wallet) {
            buyPixel(i);
        } else {
            alert("Please connect your wallet first.");
        }
    };
}

// Connect to Phantom wallet
async function connectWallet() {
    if (window.solana && window.solana.isPhantom) {
        try {
            await window.solana.connect();
            wallet = window.solana;
            document.getElementById('connect-btn').textContent = "Wallet Connected!";
            alert("Wallet connected!");
        } catch (err) {
            console.error("Connection failed", err);
        }
    } else {
        alert("Please install Phantom wallet!");
    }
}

document.getElementById('connect-btn').addEventListener('click', connectWallet);

// Buy pixel with Popcat token
async function buyPixel(pixelIndex) {
    try {
        // Create a transaction to transfer Popcat token
        const transaction = new Transaction();
        const price = 1;  // 1 Popcat = 1 pixel
        const popcatTokenAddress = new PublicKey("YOUR_POPCAT_TOKEN_ADDRESS"); // Replace with actual Popcat token address

        transaction.add(
            SystemProgram.transfer({
                fromPubkey: wallet.publicKey,
                toPubkey: popcatTokenAddress,
                lamports: price * 1000000000,  // Convert to lamports
            })
        );

        const signature = await connection.sendTransaction(transaction, [wallet]);
        await connection.confirmTransaction(signature);
        alert("Pixel purchased!");
        document.getElementById("pixel-" + pixelIndex).style.backgroundColor = "green"; // Mark as purchased
    } catch (err) {
        console.error("Purchase failed", err);
        alert("Error purchasing pixel.");
    }
}
