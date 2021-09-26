<script>
	import { onMount } from 'svelte';
	import { ethers } from 'ethers';
	import Wave from '../components/wave.svelte';
	$: account = null;
	onMount(async () => {
		try {
			const { ethereum } = window;
			if (!ethereum) {
				alert('Get MetaMask!');
				return;
			}
			const provider = new ethers.providers.Web3Provider(window.ethereum);
			const accounts = await ethereum.request({ method: 'eth_accounts' });
			if (accounts.length !== 0) {
				account = accounts[0];
				console.log('Found an authorized account:', account);
			} else {
				console.log('No authorized account found');
			}
		} catch (error) {
			console.log(error);
		}
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

{#if !account}
	<p>
		<button on:click={attachWallet}>Attach Wallet</button>
	</p>
{:else}
	<Wave />
{/if}

<style>
	h1 {
		text-align: center;
	}
	p {
		text-align: center;
	}
</style>
