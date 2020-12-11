---
description: Using ESLint to Format Javascript Code
---

# Using ESLint to Format Javascript Code

For JavaScript code we use both:

* [eslint](https://eslint.org/) - linting tool
* [Prettier](https://prettier.io/) - formatting tool

These tools can be used via the command line or right in your code editor.

## Via the Command Line

After editing some JavaScript code, navigate to the `comm/` directory. \(The following commands need to be run from the `comm/` directory so that Prettier will use the `comm/.prettierignore` file, and not the `.prettierignore` file in the directory just above `comm/`. See [Prettier issue 4081](https://github.com/prettier/prettier/issues/4081).\)

For a single file, run this command, which will attempt to automatically fix any linting or formatting problems:

```text
$ ../mach lint path/to/a/file.js --fix
```

Or for all the files in a given directory:

```text
$ ../mach lint path/to/a/directory/ --fix
```

To simply report any problems but not attempt to automatically fix them, just omit the `--fix` flag:

```text
$ ../mach lint path/to/a/file.js
```

## In a Code Editor

Most popular code editors offer plugins for eslint and Prettier. We highly recommend installing a plugin for eslint and a plugin for Prettier so you can lint and format your code as you are editing it. Issues will be highlighted as you type and you can have Prettier format your code with a few key strokes.

Here are links to plugins for various editors:

* [eslint plugins](https://eslint.org/docs/user-guide/integrations) for various editors
* [Prettier plugins](https://prettier.io/) for various editors

Some of us on the Thunderbird team use the VS Code editor with these plugins:

* [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

  VS Code plugin by Esben Petersen

* [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

  VS Code plugin by Dirk Baeumer

## More Details

* ['Linting' on firefox-source-docs](https://firefox-source-docs.mozilla.org/tools/lint/index.html)
* ['Formatting JS Code With Prettier and eslint' on MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Coding_Style/Formatting_JS_Code_With_Prettier_and_eslint)

