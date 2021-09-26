<script>
	import { onMount } from 'svelte';
	import { ethers } from 'ethers';
	$: account = null;
	onMount(async () => {
		const provider = new ethers.providers.Web3Provider(window.ethereum);
		const signer = provider.getSigner();
		try {
			account = await signer.getAddress();
		} catch (error) {}
	});
	async function attachWallet() {
		const provider = new ethers.providers.Web3Provider(window.ethereum, 'any');
		// Prompt user for account connections
		await provider.send('eth_requestAccounts', []);
		const signer = provider.getSigner();
		account = await signer.getAddress();
	}
</script>

<h1>Howdy 👋</h1>
<p>I'm Richard, this is a fun little place to wave at me on the blockchain</p>

{#if !account}
	<button on:click={attachWallet}>Attach Wallet</button>
{:else}
	<p>You're connected to the wallet!</p>
{/if}

<style>
	h1 {
		text-align: center;
	}
</style>
