<script>
	import { ethers } from "ethers";
	import { fly } from "svelte/transition";
	import contractABI from "../public/FistbumpPortal.json";
	import Popup from "./Popup.svelte";
	import FistbumpCard from "./FistbumpCard.svelte";

	const { ethereum } = window;
	const contractAddress = "0x05E8CE6b0b765e5a0fA742A2Dd46475cEeac1245";
	let loading = false;
	let accounts;
	let count = null;
	let errorDisplay;
	let success = false;
	let fistbumpsArray = [];
	let message = "";

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
			const fistbumpPortalContract = new ethers.Contract(
				contractAddress,
				contractABI.abi,
				signer
			);
			const fistbumpTxn = await fistbumpPortalContract.fistbump(message);
			await fistbumpTxn.wait();
			loading = false;
			getTotalFistbumps();
			getAllFistbumps();
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
			const fistbumpPortalContract = new ethers.Contract(
				contractAddress,
				contractABI.abi,
				provider
			);
			count = await fistbumpPortalContract.getTotalFistbumps();
		} catch (error) {
			console.log(error);
			return;
		}
	}

	async function getAllFistbumps() {
		try {
			const provider = new ethers.providers.Web3Provider(ethereum);
			const fistbumpPortalContract = new ethers.Contract(
				contractAddress,
				contractABI.abi,
				provider
			);
			const newArray = await fistbumpPortalContract.getAllFistbumps();
			fistbumpsArray = [...newArray].reverse();
		} catch (error) {
			console.log(error);
			return;
		}
	}

	function closePopup() {
		success = false;
	}

	function refresh() {
		getTotalFistbumps();
		getAllFistbumps();
	}

	refresh();
</script>

<div class="container">
	<h1>🗿 Greetings.</h1>
	<h2>
		I'm Hasan, I used to fly airplanes for a living.<br />Now all I do is
		code 💻.<br />Give me a 👊 if you love to code too.
	</h2>

	{#if !ethereum}
		<a href="https://metamask.io" target="_blank"
			><button>🦊 Get metamask</button></a
		>
	{:else if !accounts}
		{#if !loading}
			<button on:click={authorizeWallet}>🦊 Connect metamask</button>
		{:else}
			<button disabled>⏳ Connecting metamask...</button>
		{/if}
	{:else}
		<input
			type="text"
			placeholder="send me a message too if you'd like 😇"
			bind:value={message}
			disabled={!!loading}
		/> <br />
		{#if !loading}
			<button on:click={fistbump}>👊 Fistbump!</button>
		{:else}
			<button disabled
				>🤜💥🤛 Bumping fists... <span class="loading"
					><i class="fab fa-ethereum" /></span
				></button
			>
		{/if}
	{/if}

	{#if count}
		<div class="list-container">
			{#each fistbumpsArray as fistbump}
				<FistbumpCard {fistbump} />
			{/each}
		</div>
	{/if}

	{#if count}
		{#key count}<p style="margin-bottom: 0;">
				Total 👊's bumped: <span
					in:fly={{ y: 20, duration: 800 }}
					style="display: inline-block">{count}</span
				>
			</p>
		{/key}
	{:else}
		<p>connect to metamask to see all 👊's bumped!</p>
	{/if}

	<p class="refresh noselect" on:click={() => refresh()}>
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
	<Popup handleClick={closePopup} />
{/if}

<style>
	button {
		border-radius: 8px;
		padding: 0.5em 1em;
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
		padding: 2em;
	}
	input {
		width: 100%;
		margin: 1em 0;
		max-width: 400px;
		border-radius: 8px;
	}
	.list-container {
		height: 200px;
		overflow-y: scroll;
		/* From https://css.glass */
		background: rgba(0, 0, 0, 0.6);
		border-radius: 4px;
		box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
		backdrop-filter: blur(5px);
		-webkit-backdrop-filter: blur(5px);
		border: 1px solid rgba(0, 0, 0, 0.3);
		margin-top: 1em;
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
		display: inline-block;
	}
	.noselect {
		-webkit-touch-callout: none; /* iOS Safari */
		-webkit-user-select: none; /* Safari */
		-khtml-user-select: none; /* Konqueror HTML */
		-moz-user-select: none; /* Old versions of Firefox */
		-ms-user-select: none; /* Internet Explorer/Edge */
		user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */
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
