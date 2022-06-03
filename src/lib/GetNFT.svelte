<script>
	const mumbaiApiKey = import.meta.env.VITE_MUMBAI_API_KEY;
	const goerliApiKey = import.meta.env.VITE_GOERLI_API_KEY;
	const rinkebyApiKey = import.meta.env.VITE_RINKEBY_API_KEY;
	export let contract = '';
	$: data = '';
	$: nfts = [];
	$: chain = '';
	async function getResp() {
		let apiKey = '';
		switch (chain) {
			case 'polygon-mumbai':
				apiKey = mumbaiApiKey;
				break;
			case 'eth-goerli':
				apiKey = goerliApiKey;
				break;
			case 'eth-rinkeby':
				apiKey = rinkebyApiKey;
				break;
		}
		const baseURL = `https://${chain}.g.alchemy.com/v2/`;
		const getNFTs = `/getNFTsForCollection/?contractAddress=`;
		const options = `&withMetadata=true`;
		let response = await fetch(`${baseURL}${apiKey}${getNFTs}${contract}${options}`);

		data = await response.json();
		nfts = data.nfts;
	}
</script>

<input
	type="text"
	bind:value={contract}
	class="input input-bordered w-full max-w-xs"
	placeholder="Enter Contract Address"
/>
<select class="select select-bordered w-full max-w-xs" bind:value={chain}>
	<option disabled selected value="">Select The Chain</option>
	<option value="polygon-mumbai">Mumbai</option>
	<option value="eth-goerli">Goerli</option>
	<option value="eth-rinkeby">Rinkeby</option>
</select>
<button disabled={chain == '' || contract == ''} on:click={getResp} class="btn">Get NFTs </button>
<section class="mt-4 mx-auto w-10/12 relative">
	<div class="grid grid-cols-3 justify-center gap-3">
		{#each nfts as nft}
			<div class="card w-64 h-80 bg-base-100 shadow-xl">
				<figure class="px-10 pt-10 h-24">
					<img
						src={nft.metadata.image}
						alt={nft.metadata.name}
						class="rounded-xl object-contain w-10 h-24"
					/>
				</figure>
				<div class="card-body items-center text-center">
					<h2 class="card-title">{nft.title}</h2>
					<p>{nft.metadata.description}</p>
				</div>
			</div>
		{/each}
	</div>
</section>
