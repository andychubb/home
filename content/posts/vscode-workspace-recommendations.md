+++
title = 'VS Code Workspace Recommendations'
date = 2023-02-02T17:09:57Z
draft = true
tags = ['VSCode', 'Notes']
+++

There is a feature in VS Code where you can add extensions that you would recommend for use in a certain project. You can right click the extension and at the bottom select “Add to Workspace Recommendations” this will prompt somebody who clones the repo to install those extensions when they open the project in VS Code.

## Workspace Settings

As with the above you can also, add in some workspace settings in the .vscode directory. You can do this by creating a settings.json file. A couple of good ones to add in for prettier are:

```json
{
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true
}
```

This uses the Prettier VS Code plugin to format your files, and will update them every time you save.