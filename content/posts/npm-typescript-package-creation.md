+++
title = 'Npm Typescript Package Creation'
date = 2024-03-30T16:54:01Z
draft = true
tags = ['Node', 'TypeScript', 'Notes']
+++

The goal is to produce a npm package that can be imported in to either a Typescript or a JavaScript project.

## Exporting the Components

In the case of the component library in this example we’ll need to ensure that each component is exported, and can be used and imported easily. So we will need to create a series of index.ts files which do the importing. In the folder structure for this package we will have the src directory, and within here we will have the components directory which will contain all of our components. This is further split up into atoms, molecules, etc. So we need to make sure at every level we are exporting the components. 

In the src directory we’ll create an index.ts file with the following export:

`export * from './components';`

Then within our components directory, we will export each of our components out individually, for example to export our generic button component, we’ll export the following:

`export { default as GenericButton } from "./atoms/generic-button/GenericButton”`

Our GenericButton component will be a directory that contains a number of files related to the component, these will typically be:

- A .tsx file for the component itself
- A .styled.tsx file for the styled-components version of the component.
- A .stories.tsx file for the storybook story
- There may be other files in here related to testing.

We also need to create an export within this directory too which will export the component. So we’ll create another index.ts file in here with the following export:

`export { default as GenericButton } from "./GenericButton”`

## Adding in the TypeScript Config

In order for us to use this as a package, we need to introduce a .tsconfig file in the root of the project. This can be initialised by running:

`npx tsc --init`

This creates a .tsconfig file with some default values in. However, not everything is needed here, so we are replacing this with our own .tsconfig file.

```json
{
	"compilerOptions": {
		"jsx": "react", // Convert JSX Syntax into React CreateElement...
		"module": "ESNext", // Use modern ES6 type modules
		"declaration": true, // Create type declarations for use in js|ts projects
		"declarationDir": "types",
		"sourceMap": true, // Include sourcemaps for debug
		"target": "es5",
		"outDir": "dist", // Target for the bundled library
		"moduleResolution": "node",
		"allowSyntheticDefaultImports": true,
		"emitDeclarationOnly": true,
	}
}
```

## Automating Releases with Github Actions

Create an access token in NPM in order for us to use Github Actions to publish our package. Follow the instructions here (https://docs.npmjs.com/creating-and-viewing-access-tokens) to create a token.

Then we need to add this access token to our package repo, which will be hosted on Github. To do this go in to the repo settings, and then to “Secrets and Variables” the “Actions”. Create a new Repository Key, and store the value you just generated in NPM here. Name the secret `NPMJS_ACCESS_TOKEN`

Now, we can create a new Github Workflow by going to “Actions” in our repository. 

The workflow which I had working for me was as follows:

```yaml
name: Release Package
on:
  workflow_dispatch:
    inputs:
      release-type:
        description: 'Release type (one of): patch, minor, major'
        required: true
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      # Checkout the project repo
      - name: Checkout
        uses: actions/checkout@v3

      # Setup Node.js environment
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          registry-url: https://npm.pkg.github.com/
          node-version: '16'
          always-auth: true
      
      # Configure Git
      - name: Git configuration
        run: |
          git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git config --global user.name "GitHub Actions"

      # Bump package version
      # Use tag latest
      - name: Bump release version
        run: |
          echo "NEW_VERSION=$(npm --no-git-tag-version version $RELEASE_TYPE)" >> $GITHUB_ENV
          echo "RELEASE_TAG=latest" >> $GITHUB_ENV
        env:
          RELEASE_TYPE: ${{ github.event.inputs.release-type }}

      # Commit changes
      - name: Commit package.json changes and create tag
        run: |
          git add "package.json"
          git commit -m "chore: release ${{ env.NEW_VERSION }}"
          git tag ${{ env.NEW_VERSION }}
      
      # Publish version to public repository
      - name: Publish
        run: yarn publish --verbose --access public --tag ${{ env.RELEASE_TAG }}
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      # Push repository changes
      - name: Push changes to repository
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git push origin && git push --tags
```

So it’s important to note that for this I am using the Github Package Registry rather than NPM. This will effectively decide how to version your commit, based on the release type (patch, major, minor). The key values to note here are the registry url, this is set to https://npm.pkg.github.com which is where the Github Packages are registered to.  We then create a new release version based on the type of of release. We create a new tag in git and commit it. Then we use the yarn publish command to publish the package, then we update our git repo. Note that this is set to run as a workflow_dispatch, so it is only triggered manually from within the actions pane of Github. You could configure this to run on some other event for better automation. 

**NOTES**: There are slight differences between publishing this on the Github Package Registry or straight to NPM. I believe that both can be achieved within Github actions but you will need to create a secret within your github repo, which you can store with a value of NODE_AUTH_TOKEN. This allows you to publish the package from github, so you would switch out the NODE_AUTH_TOKEN value from secrets.GITHUB_TOKEN to secrets.NODE_AUTH_TOKEN. You will also need to change the publishConfig value in the package.json file so instead of: 

```json
"publishConfig": {
	"registry": "https://npm.pkg.github.com"
}
```

You will have:

```json
"publishConfig": {
  "registry": "https://registry.npmjs.org/"
}
```