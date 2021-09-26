<script>
	import { onMount } from 'svelte';
	import { ethers } from 'ethers';
	onMount(async () => {
		const provider = new ethers.providers.Web3Provider(window.ethereum);
	});
	async function attachWallet() {
		await window.ethereum.enable();
	}
</script>

<h1>Howdy</h1>
<button on:click={attachWallet}>Attach Wallet</button>
