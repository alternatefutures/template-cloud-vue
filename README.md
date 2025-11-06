<div align="center">
  <img src="./assets/hero-logo.svg" alt="Alternate Futures" width="600" />
</div>

# Vue + AlternateFutures Starter Kit

A blank starter kit for Vue.js, ready to be deployed on Alternate Futures to decentralized storage networks (IPFS, Filecoin, Arweave).

## 🚀 Project Structure

Inside of your Vue project, you'll see the following folders and files:

```
/
├── public/
│   └── favicon.svg
├── src/
│   ├── assets/
│   ├── components/
│   ├── router/
│   ├── views/
│   ├── App.vue
│   └── main.ts
├── index.html
├── env.d.ts
├── tsconfig.json
├── vite.config.ts
└── package.json
```

If you want to learn more about `vue` you can checkout the official [Vue Documentation](https://vuejs.org/guide/introduction.html).

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                | Action                                           |
| :--------------------- | :----------------------------------------------- |
| `pnpm install`          | Installs dependencies                            |
| `pnpm run dev`          | Starts local dev server at `localhost:5173`      |
| `pnpm run build`        | Build your production site to `./dist/`          |
| `pnpm run preview`      | Preview your build locally, before deploying     |
| `pnpm run build-only`      | Build your production site without running typechecks     |
| `pnpm run type-check` | Run the typecheck |

## ✨ How to Deploy to Alternate Futures

### Prerequisites

Install the Alternate Futures CLI globally:

```bash
npm install -g @alternatefutures/cli
```

Or with pnpm:

```bash
pnpm add -g @alternatefutures/cli
```

### Authentication

Before deploying, authenticate with your Alternate Futures account:

```bash
af login
```

This will open your browser to complete authentication.

### Deploy Your Site

After building your site, deploy it to your choice of decentralized storage network:

#### Deploy to IPFS

Fast, globally distributed content delivery. Best for frequently updated sites.

```bash
# Build your site first
pnpm run build

# Deploy to IPFS
af sites deploy ./dist --network ipfs
```

#### Deploy to Arweave

Permanent, immutable storage with a one-time payment. Perfect for permanent archives and NFT projects.

```bash
# Build your site first
pnpm run build

# Deploy to Arweave
af sites deploy ./dist --network arweave
```

#### Deploy to Filecoin

Cost-effective long-term storage with cryptographic proof. Ideal for large datasets.

```bash
# Build your site first
pnpm run build

# Deploy to Filecoin
af sites deploy ./dist --network filecoin
```

### Choosing the Right Network

Not sure which network to use? Here's a quick guide:

**Use IPFS if:**
- You update your site regularly
- You need fast global delivery
- You want the easiest deployment experience

**Use Arweave if:**
- Your content is permanent/immutable
- You're building an NFT project
- You want to pay once and host forever

**Use Filecoin if:**
- You have large files (100GB+)
- You need the most cost-effective storage
- You're archiving data long-term

See the [Storage Networks Guide](https://docs.alternatefutures.ai/guides/sites/#storage-networks) for detailed comparisons.

### Deployment Output

After deployment, you'll receive:

```bash
✅ Deployed successfully!

Network: ipfs
CID: QmXxx...xxx
URL: https://ipfs.io/ipfs/QmXxx...xxx
Gateway: https://gateway.alternatefutures.ai/ipfs/QmXxx...xxx

# Or for Arweave:
Network: arweave
Transaction ID: xyz123...
URL: https://arweave.net/xyz123...
```

### Using the SDK

You can also deploy programmatically using the Alternate Futures SDK:

```bash
npm install @alternatefutures/sdk
```

```typescript
import { AlternateFuturesSdk } from '@alternatefutures/sdk/node';

const af = new AlternateFuturesSdk({
  personalAccessToken: process.env.AF_TOKEN
});

// Deploy to IPFS
const deployment = await af.sites.deploy({
  name: 'My Vue App',
  network: 'ipfs',
  directory: './dist'
});

console.log('Deployed to:', deployment.url);
```

## ⚙️ Configuration Notes

### Static Output

This template has been configured to produce a static output suitable for decentralized hosting.

```js
// vite.config.ts

import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), vueJsx()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  },
  base: './',  // Assets prefixed with './' for proper IPFS/Arweave loading
})
```

This means that assets will be pre-fixed with `./`, you can learn more about it in [Vite Documentation](https://vitejs.dev/config/shared-options.html#base).

### Hash Routing for IPFS

To avoid routing issues when deploying to IPFS, this template uses Hash routing:

```js
// src/router/index.ts
import { createRouter, createWebHashHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'

const router = createRouter({
  history: createWebHashHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/about',
      name: 'about',
      component: () => import('../views/AboutView.vue')
    }
  ]
})

export default router
```

**Why Hash routing?** When a site is deployed to IPFS, URLs like `/about` won't work because IPFS serves files based on content addressing. Hash routing (`/#/about`) ensures your routes work correctly on decentralized networks.

## 🌐 Custom Domains

Point your custom domain to your deployment:

```bash
af domains create yourdomain.com --site your-site-id
```

Then configure your DNS:
- **IPFS**: Add a CNAME or DNSLink record
- **Arweave**: Add an CNAME record pointing to the Arweave gateway

See [Custom Domains Guide](https://docs.alternatefutures.ai/guides/custom-domains) for detailed instructions.

## 🔄 Continuous Deployment

Set up automated deployments on every push:

```bash
# Initialize CI/CD
af sites ci --provider github

# Or add to your GitHub Actions workflow:
```

```yaml
# .github/workflows/deploy.yml
name: Deploy to Alternate Futures

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: pnpm install

      - name: Build
        run: pnpm run build

      - name: Deploy to IPFS
        run: npx @alternatefutures/cli sites deploy ./dist --network ipfs
        env:
          AF_TOKEN: ${{ secrets.AF_TOKEN }}
```

## 📚 Learn More

- [Alternate Futures Documentation](https://docs.alternatefutures.ai/)
- [Vue Documentation](https://vuejs.org/guide/introduction.html)
- [CLI Reference](https://docs.alternatefutures.ai/cli/)
- [SDK Reference](https://docs.alternatefutures.ai/sdk/)
- [Storage Networks Guide](https://docs.alternatefutures.ai/guides/sites/#storage-networks)

## 🆘 Need Help?

- [Discord Community](https://discord.gg/alternatefutures)
- [GitHub Issues](https://github.com/alternatefutures/cloud-templates/issues)
- [Documentation](https://docs.alternatefutures.ai/)

## License

[GNU GPLv3](https://choosealicense.com/licenses/gpl-3.0/)

## Acknowledgements

The initial architecture and conceptual foundation of this project were derived from a fork of the Fleek.xyz platform.

As our project's requirements led to significant architectural changes, we have diverged from the original fork. However, we remain grateful to the Fleek team for their pioneering open-source contributions to decentralized application hosting and infrastructure.
