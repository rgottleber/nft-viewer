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
		data = '';
		let apiKey = '';
		switch (chain) {
			case 'maticmum':
				apiKey = mumbaiApiKey;
				break;
			case 'goerli':
				apiKey = goerliApiKey;
				break;
			case 'rinkeby':
				apiKey = rinkebyApiKey;
				break;
		}
		const provider = new ethers.providers.AlchemyProvider(chain, apiKey);

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
		<select class="my-4 select select-bordered w-full max-w-xs" bind:value={chain}>
			<option disabled selected value="">Select The Chain</option>
			<option value="maticmum">Mumbai</option>
			<option value="goerli">Goerli</option>
			<option value="rinkeby">Rinkeby</option>
		</select>
		<input
			type="text"
			bind:value={contractAddr}
			class="input input-bordered w-full max-w-xs my-4"
			placeholder="Enter Contract Address"
		/>
		<input
			type="text"
			bind:value={tokenID}
			class="input input-bordered w-full max-w-xs my-4"
			placeholder="Enter Token ID"
		/>
		<button on:click={connectWallet} class="btn">View NFT</button>
	</div>
	{#if data}
		<div class="flex flex-col items-center justify-center h-full">
			<div class="card bg-base-100 shadow-xl my-4 h-auto w-auto">
				<figure class="px-10 pt-10 h-48">
					<img src={image.src} alt={data.name} class="rounded-xl object-contain w-36 h-48" />
				</figure>
				<div class="card-body items-center">
					<h2 class="card-title  text-center">{data.name}</h2>
					<p class="text-center">{data.description}</p>
					<pre class="overflow-auto">
{JSON.stringify(data, null, 2)}
					</pre>
				</div>
			</div>
		</div>
	{/if}
</div>
