<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>GiwaGuard - Two-Key Security Vault</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/ethers/5.7.2/ethers.umd.min.js"></script>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #0f172a; color: white; text-align: center; padding: 20px; }
        .card { background: #1e293b; max-width: 520px; margin: 0 auto; padding: 25px; border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.5); text-align: left; }
        h1, h3 { text-align: center; color: #38bdf8; }
        .info { background: #334155; padding: 12px; border-radius: 6px; font-size: 0.85em; word-break: break-all; margin-bottom: 20px; }
        button { background: #0284c7; color: white; border: none; padding: 12px; border-radius: 6px; font-weight: bold; width: 100%; margin: 8px 0; cursor: pointer; }
        button:hover { background: #0369a1; }
        .danger { background: #dc2626; }
        .danger:hover { background: #b91c1c; }
        input { width: 95%; padding: 10px; margin: 6px 0; border-radius: 6px; border: 1px solid #334155; background: #0f172a; color: white; }
        .badge { background: #0284c7; padding: 3px 8px; border-radius: 4px; font-size: 0.75em; font-weight: bold; }
    </style>
</head>
<body>

<div class="card">
    <h1>🛡️ GiwaGuard</h1>
    <p style="text-align: center; color: #94a3b8;">Two-Key L2 Vault Security</p>

    <div class="info">
        <strong>Contract:</strong> <code>0x2AD38dB18f1C292EBF12502844837C3b7C809ac7</code><br>
        <strong>Network:</strong> GIWA Sepolia Testnet (91342)
    </div>

    <button onclick="connectWallet()">🔑 Connect Connected Wallet</button>
    <p id="walletAddress" style="color: #4ade80; text-align: center; font-size: 0.9em;"></p>

    <hr style="border-color: #334155;">

    <h3>1️⃣ Manage Safe List <span class="badge">KEY 2 ONLY</span></h3>
    <p style="font-size: 0.8em; color: #94a3b8;">Key 2 adds or wipes trusted exchange addresses.</p>
    <input type="text" id="safeAddressInput" placeholder="Enter Target Wallet Address">
    <button onclick="updateSafeList(true)">✅ Approve Address to Safe List</button>
    <button style="background: #64748b;" onclick="updateSafeList(false)">🧹 Wipe / Remove Address from Safe List</button>

    <hr style="border-color: #334155;">

    <h3>2️⃣ Emergency Vault Recovery <span class="badge">KEY 2 ONLY</span></h3>
    <p style="font-size: 0.8em; color: #f87171;">If Key 1 is hacked: Freeze contract and sweep ALL funds to Key 2 instantly.</p>
    <button class="danger" onclick="triggerEmergencyFreeze()">🚨 EMERGENCY FREEZE & RECOVER ALL FUNDS</button>
</div>

<script>
    const contractAddress = "0x2AD38dB18f1C292EBF12502844837C3b7C809ac7";
    const giwaChainId = "0x164C6"; // 91342 in Hexadecimal

    const contractABI = [
        "function updateSafeList(address _target, bool _status) public",
        "function emergencyFreeze() public"
    ];

    async function ensureGiwaNetwork() {
        if (!window.ethereum) return alert("MetaMask is required!");
        try {
            await window.ethereum.request({
                method: 'wallet_switchEthereumChain',
                params: [{ chainId: giwaChainId }],
            });
        } catch (switchError) {
            // If GIWA network is not added to MetaMask, add it automatically
            if (switchError.code === 4902) {
                await window.ethereum.request({
                    method: 'wallet_addEthereumChain',
                    params: [{
                        chainId: giwaChainId,
                        chainName: 'GIWA Sepolia Testnet',
                        rpcUrls: ['https://sepolia-rpc.giwa.io'],
                        nativeCurrency: { name: 'ETH', symbol: 'ETH', decimals: 18 }
                    }],
                });
            }
        }
    }

    async function connectWallet() {
        if (!window.ethereum) return alert("Please open using MetaMask.");
        await ensureGiwaNetwork();
        const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
        await provider.send("eth_requestAccounts", []);
        const signer = provider.getSigner();
        const address = await signer.getAddress();
        document.getElementById("walletAddress").innerText = "Active Wallet: " + address;
    }

    async function updateSafeList(status) {
        if (!window.ethereum) return;
        try {
            await ensureGiwaNetwork();
            const target = document.getElementById("safeAddressInput").value;
            if(!target) return alert("Please enter a target wallet address!");

            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(contractAddress, contractABI, signer);

            const tx = await contract.updateSafeList(target, status, { gasLimit: 300000 });
            alert("Transaction requested! Check MetaMask to confirm.");
            await tx.wait();
            alert("Success! Safe list updated on GIWA Sepolia!");
        } catch (err) {
            alert("Transaction details: " + (err.reason || err.message));
        }
    }

    async function triggerEmergencyFreeze() {
        if (!window.ethereum) return;
        try {
            await ensureGiwaNetwork();
            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(contractAddress, contractABI, signer);

            const tx = await contract.emergencyFreeze({ gasLimit: 300000 });
            alert("Emergency Freeze Triggered! Confirming transaction in wallet...");
            await tx.wait();
            alert("VAULT FROZEN! Funds swept safely to Key 2.");
        } catch (err) {
            alert("Transaction details: " + (err.reason || err.message));
        }
    }
</script>

</body>
</html>
