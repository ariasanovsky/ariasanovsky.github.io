# Personal site

Made using [Zola](https://www.getzola.org/).
Thanks [SZM](https://syedzayyan.com/) for making the theme!

## Notes on using Zola
- To minimize mishaps, `zola init [YOUR_GITHUB_NAME].github.io` when you make your site. Thank me later.
- Immediately `cd [YOUR_GITHUB_NAME].github.io` and `zola serve`.
  - This way you can watch the site render and make sure your changes are working.
- DO NOT DO A RAW CLONE of your theme. Use submodules: `git submodule add https://github.com/[REST_OF_URL] themes/[THEME_NAME]`.
- Copy content from the theme directly, bit-by-bit. It may be easier to go to the working example's github page and copy files one-by-one. Don't give up! Use the served site. Make **incremental changes**.

## Github deployment
- Follow the [deployment guide](https://www.getzola.org/documentation/deployment/github-pages/) *carefully*.
- There is currently (2022-03-31) a [regression](https://github.com/getzola/zola/issues/2150) with the `0.17.1` deployment. Do not change `v.0.16.1-1` to `17.2` or later until it's fixed.
  - This may affect your build if you are using later features.
- You probably do not need to make the token. Come back to it if you are explicitly getting a token problem and switch the `TOKEN` line in `zola-deploy-action`.
- Do not miss the line that says to switch `project > Settings > Pages > Build and deployment > Branch` to `gh-pages`.
- You may need to change `project > Settings > Actions permissions > Workflow permissions > Read and write permissions`.
- When you see a red checkmark for a failed action, rerun with debug logs and stare at the errors.
  - Token? Consider a manual token.
  - Explicit build failure? Make sure you can serve your site from a fresh clone (on the version in the action script).
- Site rendering nothing? You probably didn't switch the deployment branch to `gh-pages`.
