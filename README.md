# Astro + AlternateFutures Starter Kit

<div align="center">
  <img src="https://raw.githubusercontent.com/alternatefutures/cloud-templates/main/assets/hero-logo.svg" width="600" />
</div>

## 🚀 Project Structure

Inside of your Astro project, you'll see the following folders and files:

```
/
├── public/
│   └── favicon.svg
├── src/
│   ├── components/
│   │   └── Card.astro
│   ├── layouts/
│   │   └── Layout.astro
│   └── pages/
│       └── index.astro
└── package.json
```

Astro looks for `.astro` or `.md` files in the `src/pages/` directory. Each page is exposed as a route based on its file name.

There's nothing special about `src/components/`, but that's where we like to put any Astro/React/Vue/Svelte/Preact components.

Any static assets, like images, can be placed in the `public/` directory.

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                | Action                                           |
| :--------------------- | :----------------------------------------------- |
| `npm install`          | Installs dependencies                            |
| `npm run dev`          | Starts local dev server at `localhost:3000`      |
| `npm run build`        | Build your production site to `./dist/`          |
| `npm run preview`      | Preview your build locally, before deploying     |
| `npm run astro ...`    | Run CLI commands like `astro add`, `astro check` |
| `npm run astro --help` | Get help using the Astro CLI                     |

## ✨ How to deploy to Alternate Futures

### 1. Create a config file:
You can configure this site deployment using [Alternate Futures CLI](https://docs.alternatefutures.ai/) and running:
```
 > af sites init
  WARN! Alternate Futures CLI is in beta phase, use it under your own responsibility
   ? Choose one of the existing sites or create a new one. ›
    ❯ Create a new site
```
 It will prompt you for a `name`, `dist` directory location & `build command`

 - `name`: How you want to name the site
 - `dist`: The output directory where the site is located, for this template it's `dist`
 - `build command`: Command to build your site, this will be used to deploy the latest version either by CLI or Github Actions

### 2. Deploy the site
After configuring your site, you can deploy it by running

```
af sites deploy
```
After running it you will get an output like this:
```
 WARN! Alternate Futures CLI is in beta, use it at your own discretion
  > Success! Deployed!
   > Site IPFS CID: QmP1nDyoHqSrRabwUSrxRV3DJqiKH7b9t1tpLcr1NTkm1M

    > You can visit through the gateway:
     > https://ipfs.io/ipfs/QmP1nDyoHqSrRabwUSrxRV3DJqiKH7b9t1tpLcr1NTkm1M
```

### Extra features
- **Continuous Integration (CI):** `af sites ci` [Documentation.](https://docs.alternatefutures.ai/services/sites/#continuous-integration-ci)
- **Adding custom domains:** `af domains create` [Documentation.](https://docs.alternatefutures.ai/services/domains/)


### Keep in mind:

This template has been configured to produce a static output.

```js
// astro.config.mjs 

import { defineConfig } from 'astro/config';

// https://astro.build/config
export default defineConfig({
    output: 'static',
});
```

You can find more information about static builds in [Astro Documentation](https://docs.astro.build/en/guides/content-collections/#building-for-static-output-default)


## 👀 Want to learn more?

Feel free to check [Astro documentation](https://docs.astro.build) or jump into Astro's [Discord server](https://astro.build/chat).

## 📄 License

This project is licensed under [GNU GPLv3](https://choosealicense.com/licenses/gpl-3.0/).

## 🙏 Acknowledgements

