<script>
	import { ethers } from "ethers";
	import { fly, } from "svelte/transition";
	import contractABI from "../public/WavePortal.json";
	import Popup from './Popup.svelte'

	const { ethereum } = window;
	const contractAddress = "0xCc154123859d39047FF13eB7D61CBCCCbDE415a7";
	let loading = false;
	let accounts;
	let count = 0;
	let errorDisplay;
	let success = false;

	async function authorizeWallet() {
		try {
			loading = true;
			accounts = await ethereum.request({
				method: "eth_requestAccounts",
			});
			loading = false;
		} catch (error) {
			loading = false;
			console.log(error);
			errorDisplay = error.message;
		}
	}

	async function fistbump() {
		try {
			loading = true;
			const provider = new ethers.providers.Web3Provider(ethereum);
			const signer = provider.getSigner();
			const wavePortalContract = new ethers.Contract(
				contractAddress,
				contractABI.abi,
				signer
			);
			const waveTxn = await wavePortalContract.wave();
			await waveTxn.wait();
			loading = false;
			getTotalFistbumps();
			success = true;
		} catch (error) {
			loading = false;
			console.log(error);
			errorDisplay = error.message;
		}
	}

	async function getTotalFistbumps() {
		try {
			const provider = new ethers.providers.Web3Provider(ethereum);
			const wavePortalContract = new ethers.Contract(
				contractAddress,
				contractABI.abi,
				provider
			);
			count = await wavePortalContract.getTotalWaves();
		} catch (error) {
			console.log(error);
			return;
		}
	}

	function closePopup() {
		success = false;
	}

	getTotalFistbumps();
</script>

<div class="container">
	<h1>🗿 Greetings.</h1>
	<h2>
		I'm Hasan, I used to fly airplanes for a living.<br />Now all I do is code
		💻.<br />Give me a 👊 if you love to code too.
	</h2>

	{#if !ethereum}
		<a href="https://metamask.io" target="_blank"
			><button>🦊 Get metamask</button></a
		>
	{:else if !accounts}
		{#if loading}
			<button disabled>⏳ Connecting metamask...</button>
		{:else}
			<button on:click={authorizeWallet}>🦊 Connect metamask</button>
		{/if}
	{:else if loading}
		<button disabled
			>🤜💥🤛 Bumping fists... <span class="loading"
				><i class="fab fa-ethereum" /></span
			></button
		>
	{:else}
		<button on:click={fistbump}>👊 Fistbump!</button>
	{/if}

	{#key count}<p style="margin-bottom: 0;">
			Total 👊's bumped: <span
				in:fly={{ y: 20, duration: 800 }}
				style="display: inline-block">{count}</span
			>
		</p>{/key}
	<p class="refresh" on:click={() => getTotalFistbumps()}>
		<i class="fas fa-sync-alt" /> refresh
	</p>

	{#if errorDisplay}
		<p class="error">
			<i
				class="fas fa-times-circle"
				on:click|preventDefault={() => (errorDisplay = "")}
				style="cursor: pointer"
			/>
			Error: {errorDisplay}
		</p>
	{/if}
</div>
{#if success}
<Popup handleClick={closePopup}/>
{/if}

<style>
	button {
		border-radius: 8px;
		padding: 0.6em 1em;
	}
	h2 {
		font-weight: 500;
	}
	.container {
		position: relative;
		text-align: center;
		width: 100%;
		max-width: 500px;
		/* From https://css.glass */
		background: rgba(255, 255, 255, 0.2);
		border-radius: 16px;
		box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
		backdrop-filter: blur(3px);
		-webkit-backdrop-filter: blur(3px);
		border: 1px solid rgba(255, 255, 255, 0.3);
		padding: 1em;
	}
	.error {
		color: red;
		font-size: 0.7em;
		opacity: 0.7;
	}
	.refresh {
		opacity: 0.7;
		font-size: 0.7em;
		cursor: pointer;
	}
	.loading {
		animation: loading 2s;
		animation-iteration-count: infinite;
		/* animation-timing-function:cubic-bezier(0.6, -0.28, 0.735, 0.045); */
		display: inline-block;
	}
	@keyframes loading {
		50% {
			transform: rotate(180deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
</style>
