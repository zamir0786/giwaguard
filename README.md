<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GiwaGuard - Institutional L2 Security Vault</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/ethers/5.7.2/ethers.umd.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg-color: #030712;
            --card-bg: rgba(17, 24, 39, 0.75);
            --card-border: rgba(255, 255, 255, 0.1);
            --primary-cyan: #38bdf8;
            --accent-glow: rgba(56, 189, 248, 0.15);
            --emerald: #10b981;
            --amber: #f59e0b;
            --rose: #f43f5e;
            --text-main: #f9fafb;
            --text-muted: #9ca3af;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: radial-gradient(circle at 50% 0%, #0f172a 0%, #030712 100%);
            color: var(--text-main); 
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 30px 15px;
        }

        .container { 
            background: var(--card-bg); 
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            width: 100%;
            max-width: 560px; 
            padding: 32px; 
            border-radius: 20px; 
            border: 1px solid var(--card-border);
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.6), 0 0 40px var(--accent-glow);
        }

        .header { text-align: center; margin-bottom: 24px; }
        .logo-title { font-size: 1.8rem; font-weight: 800; color: var(--text-main); display: flex; align-items: center; justify-content: center; gap: 8px; }
        .logo-title span { color: var(--primary-cyan); }
        .subtitle { font-size: 0.85rem; color: var(--text-muted); margin-top: 4px; font-weight: 600; text-transform: uppercase; letter-spacing: 1px; }

        .network-info { 
            background: rgba(15, 23, 42, 0.6); 
            border: 1px solid rgba(255,255,255,0.05); 
            padding: 12px 16px; 
            border-radius: 12px; 
            font-size: 0.8rem; 
            word-break: break-all; 
            margin-bottom: 20px;
        }
        .network-info strong { color: var(--primary-cyan); }

        .btn { 
            background: linear-gradient(135deg, #0284c7 0%, #0369a1 100%);
            color: white; 
            border: none; 
            padding: 14px; 
            border-radius: 10px; 
            font-weight: 700; 
            font-size: 0.95rem;
            width: 100%; 
            cursor: pointer; 
            transition: all 0.2s ease-in-out;
            box-shadow: 0 4px 15px rgba(2, 132, 199, 0.3);
        }
        .btn:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(2, 132, 199, 0.5); }
        .btn-emerald { background: linear-gradient(135deg, #10b981 0%, #047857 100%); box-shadow: 0 4px 15px rgba(16, 185, 129, 0.3); }
        .btn-emerald:hover { box-shadow: 0 6px 20px rgba(16, 185, 129, 0.5); }
        .btn-amber { background: linear-gradient(135deg, #f59e0b 0%, #b45309 100%); box-shadow: 0 4px 15px rgba(245, 158, 11, 0.3); }
        .btn-amber:hover { box-shadow: 0 6px 20px rgba(245, 158, 11, 0.5); }
        .btn-slate { background: rgba(51, 65, 85, 0.8); }
        .btn-slate:hover { background: rgba(71, 85, 105, 1); }
        .btn-danger { background: linear-gradient(135deg, #f43f5e 0%, #be123c 100%); box-shadow: 0 4px 15px rgba(244, 63, 94, 0.3); }
        .btn-danger:hover { box-shadow: 0 6px 20px rgba(244, 63, 94, 0.5); }

        .wallet-status { text-align: center; font-size: 0.85rem; color: #4ade80; margin: 10px 0; font-weight: 600; word-break: break-all; }

        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin: 20px 0; }
        .stat-card { 
            background: rgba(15, 23, 42, 0.8); 
            border: 1px solid rgba(255,255,255,0.08); 
            padding: 16px; 
            border-radius: 12px; 
            text-align: center;
        }
        .stat-card span { font-size: 0.75rem; color: var(--text-muted); font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; }
        .stat-card h2 { font-size: 1.3rem; margin-top: 6px; font-weight: 800; color: var(--text-main); }

        .section-title { font-size: 1rem; font-weight: 700; color: var(--text-main); margin-bottom: 6px; display: flex; align-items: center; justify-content: space-between; }
        .section-desc { font-size: 0.8rem; color: var(--text-muted); margin-bottom: 12px; }
        
        input { 
            width: 100%; 
            padding: 12px 14px; 
            margin-bottom: 10px; 
            border-radius: 10px; 
            border: 1px solid rgba(255, 255, 255, 0.1); 
            background: rgba(3, 7, 18, 0.6); 
            color: white; 
            font-family: inherit;
            font-size: 0.9rem;
            outline: none;
            transition: border 0.2s;
        }
        input:focus { border-color: var(--primary-cyan); }

        .badge { background: rgba(56, 189, 248, 0.15); color: var(--primary-cyan); padding: 4px 8px; border-radius: 6px; font-size: 0.7rem; font-weight: 700; }
        .divider { height: 1px; background: rgba(255, 255, 255, 0.08); margin: 24px 0; }
    </style>
</head>
<body>

<div class="container">
    <div class="header">
        <div class="logo-title">🛡️ Giwa<span>Guard</span></div>
        <div class="subtitle">Two-Key L2 Vault Protocol</div>
    </div>

    <div class="network-info">
        <strong>Vault Contract:</strong> <span id="displayContract">0xCdA1412e7be76aB6C8cb7f7e2C746AC9A6bda535</span><br>
        <strong>Network:</strong> GIWA Sepolia Testnet (91342)
    </div>

    <button class="btn" onclick="connectWallet()">🔑 Connect Wallet</button>
    <div id="walletAddress" class="wallet-status"></div>

    <div class="stats-grid">
        <div class="stat-card">
            <span>Vault Balance</span>
            <h2 id="vaultBalance">0.00 ETH</h2>
        </div>
        <div class="stat-card">
            <span>Safe List Count</span>
            <h2 id="whitelistCount">0</h2>
        </div>
    </div>

    <div class="divider"></div>

    <div class="section-title">📥 Deposit Funds</div>
    <div class="section-desc">Add native ETH into the vault contract.</div>
    <input type="number" id="depositAmount" placeholder="Amount in ETH (e.g. 0.001)" step="0.0001">
    <button class="btn btn-emerald" onclick="depositETH()">💰 Deposit ETH</button>

    <div class="divider"></div>

    <div class="section-title">1️⃣ Manage Safe List <span class="badge">KEY 2 ONLY</span></div>
    <div class="section-desc">Key 2 pre-approves or revokes trusted withdrawal destinations.</div>
    <input type="text" id="safeAddressInput" placeholder="Target Wallet Address (0x...)">
    <button class="btn" style="margin-bottom: 8px;" onclick="updateSafeList(true)">✅ Approve Address</button>
    <button class="btn btn-slate" onclick="updateSafeList(false)">🧹 Wipe / Revoke Address</button>

    <div class="divider"></div>

    <div class="section-title">2️⃣ Safe Withdrawal <span class="badge">KEY 1 (SAFE LIST)</span></div>
    <div class="section-desc">Transfer funds out (Strictly blocked if recipient is unapproved).</div>
    <input type="text" id="withdrawRecipient" placeholder="Recipient Address">
    <input type="number" id="withdrawAmount" placeholder="Amount in ETH" step="0.0001">
    <button class="btn btn-amber" onclick="withdrawETH()">📤 Withdraw Funds</button>

    <div class="divider"></div>

    <div class="section-title">3️⃣ Emergency Vault Recovery <span class="badge">KEY 2 ONLY</span></div>
    <div class="section-desc" style="color: #fca5a5;">If Key 1 is compromised: Instantly freeze vault and sweep all funds.</div>
    <button class="btn btn-danger" onclick="triggerEmergencyFreeze()">🚨 EMERGENCY FREEZE & SWEEP</button>
</div>

<script>
    const rawContractAddress = "0xCdA1412e7be76aB6C8cb7f7e2C746AC9A6bda535";
    
    const contractABI = [
        "function updateSafeList(address _target, bool _status) public",
        "function emergencyFreeze() public",
        "function safeTransfer(address payable _to, uint256 _amount) public",
        "function getWhitelistedCount() public view returns (uint256)",
        "function isWhitelisted(address) public view returns (bool)"
    ];

    function safeChecksum(addr) {
        try {
            return ethers.utils.getAddress(addr);
        } catch (e) {
            return ethers.utils.getAddress(addr.toLowerCase());
        }
    }

    async function updateDashboard() {
        if (!window.ethereum) return;
        try {
            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const formattedContract = safeChecksum(rawContractAddress);
            
            const rawBalance = await provider.getBalance(formattedContract);
            const ethBalance = ethers.utils.formatEther(rawBalance);
            document.getElementById("vaultBalance").innerText = parseFloat(ethBalance).toFixed(4) + " ETH";

            const contract = new ethers.Contract(formattedContract, contractABI, provider);
            const count = await contract.getWhitelistedCount();
            document.getElementById("whitelistCount").innerText = count.toString();
        } catch(e) {
            console.log("Dashboard fetch error:", e);
        }
    }

    async function connectWallet() {
        if (!window.ethereum) return alert("Please open using MetaMask.");
        const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
        await provider.send("eth_requestAccounts", []);
        const signer = provider.getSigner();
        const address = await signer.getAddress();
        document.getElementById("walletAddress").innerText = "Active Wallet: " + address;
        await updateDashboard();
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

            alert("Deposit transaction submitted!");
            await tx.wait();
            alert("Funds successfully deposited into Vault!");
            await updateDashboard();
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
            alert("Whitelist transaction requested!");
            await tx.wait();
            alert("Success! Safe list updated!");
            await updateDashboard();
        } catch (err) {
            alert("Transaction failed: Only Key 2 can update whitelist!");
        }
    }

    async function withdrawETH() {
        if (!window.ethereum) return alert("Please connect wallet.");
        try {
            const recipient = document.getElementById("withdrawRecipient").value.trim();
            const amount = document.getElementById("withdrawAmount").value;
            if(!recipient || !amount) return alert("Enter recipient address and amount!");

            const formattedRecipient = safeChecksum(recipient);
            const formattedContract = safeChecksum(rawContractAddress);

            const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
            const signer = provider.getSigner();
            const contract = new ethers.Contract(formattedContract, contractABI, signer);

            const approved = await contract.isWhitelisted(formattedRecipient);
            if(!approved) {
                return alert("⛔ SECURITY BLOCK: This recipient address is NOT whitelisted by Key 2! Transaction blocked.");
            }

            const weiAmount = ethers.utils.parseEther(amount);
            const tx = await contract.safeTransfer(formattedRecipient, weiAmount, { gasLimit: 300000 });

            alert("Withdrawal requested! Confirming...");
            await tx.wait();
            alert("🎉 Withdrawal Successful! Real ETH sent from Vault!");
            await updateDashboard();
        } catch (err) {
            alert("Withdrawal blocked by smart contract!");
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
            alert("Emergency Freeze Triggered!");
            await tx.wait();
            alert("🚨 VAULT FROZEN! Funds swept safely to Key 2.");
            await updateDashboard();
        } catch (err) {
            alert("Transaction failed: Only Key 2 can trigger freeze!");
        }
    }
</script>

</body>
</html>
