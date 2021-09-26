<!-- WavePortal address:  0x60976Cf4146309858354D6067185b5DE25c10549 -->
<script>
	import { ethers } from 'ethers';
	import waveportal from '../utils/WavePortal.json';
	$: waveCount = 0;
	$: mining = false;
	const contractAddress = '0x60976Cf4146309858354D6067185b5DE25c10549';
	const getWaves = async () => {
		try {
			const { ethereum } = window;

			if (ethereum) {
				const provider = new ethers.providers.Web3Provider(ethereum);
				const signer = provider.getSigner();
				const waveportalContract = new ethers.Contract(contractAddress, waveportal.abi, signer);

				let count = await waveportalContract.getTotalWaves();
				waveCount = count;
				console.log('Retrieved total wave count...', count.toNumber());
			} else {
				console.log("Ethereum object doesn't exist!");
			}
		} catch (error) {
			console.log(error);
		}
	};
	const wave = async () => {
		try {
			const { ethereum } = window;

			if (ethereum) {
				const provider = new ethers.providers.Web3Provider(ethereum);
				const signer = provider.getSigner();
				const waveportalContract = new ethers.Contract(contractAddress, waveportal.abi, signer);

				let count = await waveportalContract.getTotalWaves();
				console.log('Retrieved total wave count...', count.toNumber());
				const waveTxn = await waveportalContract.wave();
				mining = true;
				console.log('Mining...', waveTxn.hash);
				await waveTxn.wait();
				mining = false;
				console.log('Mined -- ', waveTxn.hash);

				count = await waveportalContract.getTotalWaves();
				waveCount = count;
				console.log('Retrieved total wave count...', count.toNumber());
			} else {
				console.log("Ethereum object doesn't exist!");
			}
		} catch (error) {
			console.log(error);
		}
	};
	getWaves();
</script>

<p>👋 Hello, I'm a 🤖 created by @waveportal.</p>
<p>{waveCount} 🧔s have waved</p>

{#if !mining}
	<p><button on:click={wave}>👋 at me please!</button></p>
{:else}
	<p>🤖 is currently mining... ⛏️⛏️⛏️</p>
{/if}

<style>
	p {
		text-align: center;
	}
</style>
