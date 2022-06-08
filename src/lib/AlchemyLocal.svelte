<script>
	import { ethers } from 'ethers';
	const mumbaiApiKey = import.meta.env.VITE_MUMBAI_API_KEY;
	const goerliApiKey = import.meta.env.VITE_GOERLI_API_KEY;
	const rinkebyApiKey = import.meta.env.VITE_RINKEBY_API_KEY;
	import NFTAbi from '../contracts/NFT.json';
	$: chain = '';
	$: contractAddr = '';
	$: data = '';
	$: tokenID = '';
	$: image = '';
	async function connectWallet() {
		let apiKey = '';
		switch (chain) {
			case 'mumbai':
				apiKey = mumbaiApiKey;
				break;
			case 'goerli':
				apiKey = goerliApiKey;
				break;
			case 'rinkeby':
				apiKey = rinkebyApiKey;
				break;
		}
		const provider = new ethers.providers.AlchemyProvider(chain, goerliApiKey);

		const contract = new ethers.Contract(contractAddr, NFTAbi.abi, provider);
		let URI = await contract.tokenURI(tokenID);
		let json = await fetch(URI);
		let tempData = await json.json();
		const preloadImage = (src) =>
			new Promise((r) => {
				image = new Image();
				image.onload = r;
				image.onerror = r;
				image.src = src;
			});
		await preloadImage(tempData.image);
		data = tempData;
	}
</script>

<div class="flex flex-col items-center justify-center h-full">
	<div class="w-full max-w-xs">
		<select class="select select-bordered w-full max-w-xs" bind:value={chain}>
			<option disabled selected value="">Select The Chain</option>
			<option value="mumbai">Mumbai</option>
			<option value="goerli">Goerli</option>
			<option value="rinkeby">Rinkeby</option>
		</select>
		<input
			type="text"
			bind:value={contractAddr}
			class="input input-bordered w-full max-w-xs"
			placeholder="Enter Contract Address"
		/>
		<input
			type="text"
			bind:value={tokenID}
			class="input input-bordered w-full max-w-xs"
			placeholder="Enter Token ID"
		/>
		<button on:click={connectWallet} class="btn">View NFT</button>
		{#if data}
			<div class="card w-80 h-96 bg-base-100 shadow-xl">
				<figure class="px-10 pt-10 h-48">
					<img src={image.src} alt={data.name} class="rounded-xl object-contain w-24 h-48" />
				</figure>
				<div class="card-body items-center text-center">
					<h2 class="card-title">{data.name}</h2>
					<p>{data.description}</p>
				</div>
			</div>
		{/if}
	</div>
</div>
