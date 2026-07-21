<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>GiwaGuard - L2 Vault Security</title>
    <style>
        body { font-family: sans-serif; background: #0f172a; color: white; text-align: center; padding: 40px; }
        .card { background: #1e293b; max-width: 480px; margin: 0 auto; padding: 30px; border-radius: 12px; }
        h1 { color: #38bdf8; }
        button { background: #0284c7; color: white; border: none; padding: 12px; border-radius: 6px; font-weight: bold; width: 100%; margin: 10px 0; cursor: pointer; }
        .danger { background: #dc2626; }
        input { width: 90%; padding: 10px; margin: 8px 0; border-radius: 6px; border: 1px solid #334155; background: #0f172a; color: white; }
        .info { background: #334155; padding: 10px; border-radius: 6px; font-size: 0.85em; word-break: break-all; margin-bottom: 20px; }
    </style>
</head>
<body>

<div class="card">
    <h1>🛡️ GiwaGuard</h1>
    <p>Institutional Safety Vault on GIWA L2</p>

    <div class="info">
        <strong>Contract Address (GIWA Sepolia):</strong><br>
        <code>0x2AD...09ac7</code>
    </div>

    <button onclick="connect()">1. Connect Wallet</button>
    <p id="status" style="color: #4ade80;"></p>

    <hr style="border-color: #334155; margin: 20px 0;">

    <h3>Manage Safe List (Key 2 Only)</h3>
    <input type="text" placeholder="Paste Exchange Wallet Address">
    <button onclick="alert('Transaction requested! Please confirm in your connected wallet.')">Add Address to Safe List</button>

    <hr style="border-color: #334155; margin: 20px 0;">

    <h3>Emergency Panic Button</h3>
    <button class="danger" onclick="alert('EMERGENCY FREEZE TRIGGERED! Confirming this in your wallet will lock all vault transfers immediately.')">🔒 EMERGENCY FREEZE VAULT</button>
</div>

<script>
    async function connect() {
        if (window.ethereum) {
            const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
            document.getElementById('status').innerText = "Connected: " + accounts[0].substring(0,6) + "..." + accounts[0].substring(38);
        } else {
            alert("Please open this page in a browser with a Web3 wallet (MetaMask or OKX).");
        }
    }
</script>

</body>
</html>
