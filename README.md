# W3Vm Wallet Connectors Library

<a href="https://w3vm-docs.vercel.app/" target="_blank">Read the Full Documentation</a>

<a href="https://w3vm.vercel.app/" target="_blank">General Example</a>

<a href="https://w3vm-web3modal.vercel.app/" target="_blank">W3Vm with Web3Modal & Ethers</a>

# Getting Started

W3Vm is an evm wallet connectors library for decentralized applications. It's inspired by <a href="https://github.com/wagmi-dev/references" target="_blank">Wagmi's references</a> with the difference that it's eth-lib agnostic. (Eth-lib for ethereum libraries such as ethers.js, viem or web3.js).<br/>
It sets up for you a wallet connection infrastructure with a built-in store and React hooks to handle the wallet state and user's sessions.

**Compatible with <a href="https://docs.ethers.org/v6/" target="_blank">ethers.js</a>, <a href="https://viem.sh/" target="_blank">viem</a> and <a href="https://docs.web3js.org/" target="_blank">Web3.js</a>**

### Current supported protocols & wallets
The core package of this library supports **injected** *(browser extension)* and **EIP-6963** compliant wallet.

Additional packages that can be optionally installed:
- **WalletConnect** v2 connector
- **Coinbase SDK** connector
- **MetaMask SDK** connector

### Install

```bash
npm i @w3vm/react
```

### Install WalletConnect Connector

```bash
npm i @w3vm/walletconnect
```

### Init W3

```tsx
import { W3, initW3, Injected } from '@w3vm/react'
import { WalletConnect } from '@w3vm/walletconnect'

/* Icons */
import walletconnect from 'public/walletconnect.svg'
import wallet from 'public/extension-wallet.png'

/* WalletConnect Project Id */
const projectId = 'YOUR_PROJECT_ID'

const w3props = initW3({
  connectors: [
    new Injected({ icon: wallet }), 
    new WalletConnect({ projectId, icon: walletconnect, showQrModal: true, chains:[1, 137] })
  ],
  SSR: true, // For SSR Frameworks like Next.js
})

export default function App({ Component, pageProps }: AppProps) {
  return (
    <>
      <W3 {...w3props} /> { /* Required when SSR: true */ }
      <Component {...pageProps} />
    </>
  )
}
```

Create your WalletConnect Project ID at <a href='https://cloud.walletconnect.com/sign-in' target='_blank' >WalletConnect Cloud</a>

### Connect to a Wallet

Import the `useConnect` hook and map through the `connectors` array:
```tsx
import { useConnect } from '@w3vm/react'

export default function Connect() {

  const { connectors, connectW3, disconnectW3, wait } = useConnect()
  
  return (
    <>
      {connectors.map((connector) =>(
      <button key={connector.id} disabled={Boolean(wait)} onClick={()=>connectW3({ connector })}>
        <img src={connector.icon} alt={connector.name} />
        {connector.name}
      </button>
      ))}
    </>
  )
}
```

### Custom Hooks

```tsx
import { getW3Chain, getW3Address, getW3Error } from '@w3vm/react'

export default function UserInfo(){
  
  const address = getW3Address()
  const chain = getW3Chain()
  const error = getW3Error()
  
  return (
    <div>
      address: {address}
      <br/>
      Chain ID: {chain}
      <br/>
      Error: {error}
    </div>
  )
}
```

### Use with <a href="https://docs.ethers.org/v6/" target="_blank">ethers.js</a>
```tsx
import { BrowserProvider } from 'ethers'
import { getW3Provider } from '@w3vm/react'

export default function useEthersProvider(){

  const w3Provider = getW3Provider()

  function callContract(){
    if(!w3Provider) throw new Error('User not connected')

    const provider = new BrowserProvider(w3Provider)
    //...
  }

  //...
}
```

### Use with <a href="https://docs.web3js.org/" target="_blank">Web3.js</a>
```tsx
import Web3 from 'web3'
import { getW3Provider } from '@w3vm/react'

export default function useWeb3Provider() {

  const w3Provider = getW3Provider()

  function callContract(){
    if(!w3Provider) throw new Error('User not connected')

    const provider = new Web3(w3Provider)
    //...
  }

  //...
}
```

### Use with <a href="https://viem.sh/" target="_blank">viem</a>
```tsx
import { getW3Provider } from '@w3vm/react'
import { createWalletClient, custom } from 'viem'
import { mainnet } from 'viem/chains'

export default function useWalletClient() {

  const w3Provider = getW3Provider()

  function callContract(){
    if(!w3Provider) throw new Error('User not connected')

    const client = createWalletClient({
      chain: mainnet,
      transport: custom(w3Provider)
    })
    //...
  }

  //...
}
```