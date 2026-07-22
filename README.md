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
        .balance-box { background: #0284c7; padding: 15px; border-radius: 8px; text-align: center; margin-bottom: 15px; }
        button { background: #0284c7; color: white; border: none; padding: 12px; border-radius: 6px; font-weight: bold; width: 100%; margin: 8px 0; cursor: pointer; }
        button:hover { background: #0369a1; }
        .danger { background: #dc2626; }
        .danger:hover { background: #b91c1c; }
        input { width: 95%; padding: 10px; margin: 6px 0; border-radius: 6px; border: 1px solid #334155; background: #0f172a; color: white; }
        .badge { background: #38bdf8; color: #0f172a; padding: 3px 8px; border-radius: 4px; font-size: 0.75em; font-weight: bold; }
    </style>
</head>
<body>

<div class="card">
    <h1>🛡️ GiwaGuard Vault</h1>
    <p style="text-align: center; color: #94a3b8;">Two-Key L2 Vault Security</p>

    <div class="info">
        <strong>Contract:</strong> <code>0x2AD38dB18f1C292EBF12502844837C3b7C809ac7</code><br>
        <strong>Network:</strong> GIWA Sepolia Testnet (91342)
    </div>

    <button onclick="connectWallet()">🔑 Connect Wallet</button>
    <p id="walletAddress" style="color: #4ade80; text-align: center; font-size: 0.9em;"></p>

    <div class="balance-box">
        <span style="font-size: 0.9em; text-transform: uppercase; letter-spacing: 1px;">Vault Balance</span>
        <h2 id="vaultBalance" style="margin: 5px 0 0 0;">0.00 ETH</h2>
    </div>

    <hr style="border-color: #334155;">

    <h3>📥 Deposit / Add Funds</h3>
    <input type="number" id="depositAmount" placeholder="Amount in ETH (e.g. 0.001)" step="0.0001">
    <button style="background: #10b981;" onclick="depositETH()">💰 Deposit ETH into Vault</button>

    <hr style="border-color: #334155;">

    <h3>1️⃣ Manage Safe List <span class="badge">KEY 2 ONLY</span></h3>
    <p style="font-size: 0.8em; color: #94a3b8;">Key 2 pre-approves or wipes trusted withdrawal addresses.</p>
    <input type="text" id="safeAddressInput" placeholder="Enter Target Wallet Address">
    <button onclick="updateSafeList(true)">✅ Approve Address to Safe List</button>
    <button style="background: #64748b;" onclick="updateSafeList(false)">🧹 Wipe Address from Safe List</button>

    <hr style="border-color: #334155;">

    <h3>2️⃣ Withdraw / Remove Funds <span class="badge">KEY 1 (SAFE LIST)</span></h3>
    <p style="font-size: 0.8em; color: #94a3b8;">Transfer funds out (Only allowed to Key 2 whitelisted addresses!).</p>
    <input type="text" id="withdrawRecipient" placeholder="Recipient Address (Must be Whitelisted)">
    <input type="number" id="withdrawAmount" placeholder="Amount in ETH" step="0.0001">
    <button style="background: #f59e0b;" onclick="withdrawETH()">📤 Withdraw / Send Funds</button>

    <hr style="border-color: #334155;">

    <h3>3️⃣ Emergency Vault Recovery <span class="badge">KEY 2 ONLY</span></h3>
    <p style="font-size: 0.8em; color: #f87171;">If Key 1 is hacked: Freeze contract and sweep ALL funds to Key 2 instantly.</p>
    <button class="danger" onclick="triggerEmergencyFreeze()">🚨 EMERGENCY FREEZE & RECOVER ALL FUNDS</button>
</div>

<script>
    const rawContractAddress = "0x2AD38dB18f1C292EBF12502844837C3b7C809ac7";
    
    const contractABI = [
        "function updateSafeList(address _target, bool _status) public",
        "function emergencyFreeze() public",
        "function safeTransfer(address payable _to, uint256 _amount) public"
    ];

    function safeChecksum(addr) {
        try {
            return ethers.utils.getAddress(addr);
        } catch (e) {
            return ethers.utils.getAddress(addr.toLowerCase());
        }
    }

    async function loadBalance() {
        if (!window.ethereum) return;
        const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
        const formattedContract = safeChecksum(rawContractAddress);
        const rawBalance = await provider.getBalance(formattedContract);
        const ethBalance = ethers.utils.formatEther(rawBalance);
        document.getElementById("vaultBalance").innerText = parseFloat(ethBalance).toFixed(4) + " ETH";
    }

    async function connectWallet() {
        if (!window.ethereum) return alert("Please open using MetaMask.");
        const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
        await provider.send("eth_requestAccounts", []);
        const signer = provider.getSigner();
        const address = await signer.getAddress();
        document.getElementById("walletAddress").innerText = "Active Wallet: " + address;
        await loadBalance();
    }

    async function depositETH() {
        if (!window.ethereum) return alert("Please connect wallet.");
        try {
            const amount = document.getElementById("depositAmount").value;
            if(!amount || amount <= 0) return alert("Enter a valid ETH amount!");

            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const formattedContract = safeChecksum(rawContractAddress);

            const tx = await signer.sendTransaction({
                to: formattedContract,
                value: ethers.utils.parseEther(amount)
            });

            alert("Deposit transaction submitted! Confirm in MetaMask.");
            await tx.wait();
            alert("Funds successfully deposited into Vault!");
            await loadBalance();
        } catch (err) {
            alert("Deposit error: " + (err.reason || err.message));
        }
    }

    async function updateSafeList(status) {
        if (!window.ethereum) return alert("Please connect wallet.");
        try {
            let targetInput = document.getElementById("safeAddressInput").value.trim();
            if(!targetInput) return alert("Please enter a target wallet address!");

            const formattedTarget = safeChecksum(targetInput);
            const formattedContract = safeChecksum(rawContractAddress);

            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(formattedContract, contractABI, signer);

            const tx = await contract.updateSafeList(formattedTarget, status, { gasLimit: 300000 });
            alert("Transaction requested! Check MetaMask to confirm.");
            await tx.wait();
            alert("Success! Safe list updated on GIWA Sepolia!");
        } catch (err) {
            alert("Transaction details: " + (err.reason || err.message));
        }
    }

    async function withdrawETH() {
        if (!window.ethereum) return alert("Please connect wallet.");
        try {
            const recipient = document.getElementById("withdrawRecipient").value.trim();
            const amount = document.getElementById("withdrawAmount").value;
            if(!recipient || !amount) return alert("Enter both recipient address and amount!");

            const formattedRecipient = safeChecksum(recipient);
            const formattedContract = safeChecksum(rawContractAddress);

            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(formattedContract, contractABI, signer);

            const weiAmount = ethers.utils.parseEther(amount);
            const tx = await contract.safeTransfer(formattedRecipient, weiAmount, { gasLimit: 300000 });

            alert("Withdrawal requested! Confirming in MetaMask...");
            await tx.wait();
            alert("Withdrawal successful! Funds sent to whitelisted address.");
            await loadBalance();
        } catch (err) {
            alert("Withdrawal blocked by contract: Address is either not whitelisted or sender is unauthorized!");
        }
    }

    async function triggerEmergencyFreeze() {
        if (!window.ethereum) return alert("Please connect wallet.");
        try {
            const formattedContract = safeChecksum(rawContractAddress);
            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(formattedContract, contractABI, signer);

            const tx = await contract.emergencyFreeze({ gasLimit: 300000 });
            alert("Emergency Freeze Triggered! Confirming transaction in wallet...");
            await tx.wait();
            alert("VAULT FROZEN! Funds swept safely to Key 2.");
            await loadBalance();
        } catch (err) {
            alert("Transaction details: " + (err.reason || err.message));
        }
    }
</script>

</body>
</html>
