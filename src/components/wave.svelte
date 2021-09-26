<!-- WavePortal address:  0x60976Cf4146309858354D6067185b5DE25c10549 -->
<script>
	import { ethers } from 'ethers';
	import waveportal from '../utils/WavePortal.json';
	$: allWaves = [];
	$: mining = false;
	$: totalPrizes = 0;
	let message = '⌨️ type here';
	// const contractAddress = '0x9F0D995A8928b01305064676A7574dE5e639C014';
	// const contractAddress = '0x615980941f5839Ec589B87d8edDE831144fF598a';
	const contractAddress = '0x8968AFa4816B38Da747214Ba8995bC116c17440E';
	const cleanWaves = async (waves) => {
		/*
		 * We only need address, timestamp, and message in our UI so let's
		 * pick those out
		 */
		const cleanedWaves = [];
		waves.forEach((wave) => {
			cleanedWaves.push({
				address: wave.waver,
				timestamp: new Date(wave.timestamp * 1000),
				message: wave.message
			});
		});
		return cleanedWaves;
	};

	const getWaves = async () => {
		try {
			const { ethereum } = window;

			if (ethereum) {
				const provider = new ethers.providers.Web3Provider(ethereum);
				const signer = provider.getSigner();
				const waveportalContract = new ethers.Contract(contractAddress, waveportal.abi, signer);

				/*
				 * Call the getAllWaves method from your Smart Contract
				 */
				const waves = await waveportalContract.getAllWaves();
				totalPrizes = await provider.getBalance(contractAddress);
				/*
				 * Store our data in React State
				 */
				allWaves = (await cleanWaves(waves)).reverse();
				console.log(allWaves);
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
				const waveTxn = await waveportalContract.wave(message);
				mining = true;
				console.log('Mining...', waveTxn.hash);
				await waveTxn.wait();
				mining = false;
				console.log('Mined -- ', waveTxn.hash);
				const waves = await waveportalContract.getAllWaves();
				totalPrizes = await provider.getBalance(contractAddress);
				allWaves = (await cleanWaves(waves)).reverse();
				console.log('Retrieved total wave count...', waves.length);
				console.log(allWaves);
			} else {
				console.log("Ethereum object doesn't exist!");
			}
		} catch (error) {
			console.log(error);
		}
	};
	getWaves();
</script>

<p>👋 Hello, I'm a 🤖 created by Richard.</p>
<p>{allWaves.length} 👤s have waved</p>
<p>
	{#if totalPrizes > 0}
		<h2>🤑</h2>
	{:else}
		<h2>💸 -- <span class="message">Ξ</span> has flown the coop -- 💸</h2>
	{/if}
</p>
{#if totalPrizes > 0}
	<span class="message">{ethers.utils.formatEther(totalPrizes)} Ξ</span> available in prizes!
	{#if !mining}
		<p>
			Post a message: <input type="text" bind:value={message} />
		</p>
		<p>
			<button on:click={wave}>Click to 👋 at me!</button>
		</p>
	{:else}
		<p>🤖 is currently mining...</p>
		<h2>
			<span class="mining">⛏️⛏️⛏️</span>
		</h2>
	{/if}
{:else}
	<p>Thanks for checking it out. I might fund again soon.</p>
	<p>If you want to add funds the address is:</p>
	<p><span class="message">{contractAddress}</span></p>
{/if}

<h2>The most recent waves</h2>
{#each allWaves.slice(0, 10) as wave}
	<div class="message">
		<div>{wave.timestamp}</div>
		<div>{wave.address} says:</div>
		<div>{wave.message}</div>
	</div>
{/each}

<style>
	h2 {
		text-align: center;
	}
	p {
		text-align: center;
	}
	.mining {
		background-color: darkgoldenrod;
	}
	.message {
		background-color: darkorchid;
		margin-top: 16px;
		padding: 8px;
	}
</style>
