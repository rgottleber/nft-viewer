<script>
	import { ethers } from 'ethers';
	export let web3Props = {
		provider: null,
		signer: null,
		account: null,
		chainId: null,
		// new prop
		contract: null
	};

	async function connectWallet() {
		let provider = new ethers.providers.Web3Provider(window.ethereum, 'any');
		await provider.send('eth_requestAccounts', []);
		const signer = provider.getSigner();
		const account = await signer.getAddress();
		const chainId = await signer.getChainId();

		// new value for contract
		web3Props = { signer, provider, chainId, account };
	}
</script>

<button on:click={connectWallet}>Attach Wallet</button>
