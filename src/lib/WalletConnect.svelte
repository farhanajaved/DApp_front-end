<script lang="ts">
    import { ethers } from 'ethers';
    export let web3Props: Web3Props;

    async function connectWallet() {
        let provider = new ethers.providers.Web3Provider(window.ethereum, 'any');
        await provider.send('eth_requestAccounts', []);
        const signer = provider.getSigner();
        const account = await signer.getAddress();
        const chainId = await signer.getChainId();
        web3Props = { signer, provider, chainId, account };
    }
</script>

<h1 style="font-size: 24px; font-weight: bold;">Marketplace DApp</h1>

{#if !web3Props?.account}
    <button class="btn" on:click={connectWallet}>Attach Wallet to Register</button>
{/if}
