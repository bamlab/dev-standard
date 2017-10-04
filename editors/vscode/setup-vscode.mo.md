# [MO] Setup VSCode

## Owner: [Yann Leflour](https://github.com/yleflour)
## Steps

### Installation
Download and Install [Vscode](https://code.visualstudio.com/)

### Base Setup

#### 1. The `code` command

- Hit `cmd+shift+p`
- Select `Shell Command: Install command 'code' in PATH`
- You are now able to launch `code <path>` in your terminal

#### 2. Base plugins

Open the plugins menu and install:

- ESLint *(Dirk Baeumer)*
- Flow Language Support *(flowtype)*
- npm Intellisense *(Christian Kohler)*
- Path Intellisense *(Christian Kohler)*
- Prettier *(Esben Petersen)*
- React Native Tools *(Visual Studio Mobile Tools)*
- Auto Close Tag *(Jun Han)*
- Git Lens *(Eric Amodio)*
- Jest *(Orta)*
- Color Highlight *(Sergii Naumov)*

#### 3. Base preferences

- Hit `cmd+shift+p`
- Select `Preferences: Open User Settings`
- Add the following:
```json
"editor.tabSize": 2,
"files.insertFinalNewline": true,
"flow.useNPMPackagedFlow": true,
"workbench.iconTheme": "vs-seti",
"files.associations": {
  "Fastfile": "ruby"
}
```
