# ✅ Guide to Creating `.exe` from Electron Forge + Webpack + TypeScript Template

## 📁 Project Structure Overview (after setup)

```
your-app/
├── forge.config.ts               # Forge + Webpack plugin setup
├── webpack.main.config.ts        # Main process bundling config
├── webpack.renderer.config.ts    # Renderer bundling config
├── src/                          # Your app code
│   ├── index.ts                  # Electron main process entry
│   ├── preload.ts                # Preload scripts
│   ├── renderer.ts               # Renderer (browser window) logic
│   ├── index.html                # App HTML
├── out/make/                     # 🔥 Output .exe and zip live here
└── ...
```

---

## 🛠 1. **Install Electron Forge with Webpack & TS Template**

If not already done:

```bash
npx create-electron-app my-app --template=webpack-typescript
cd my-app
```

---

## ⚙️ 2. **Edit `forge.config.ts` to Configure Makers**

Ensure your `forge.config.ts` contains **Squirrel and ZIP** makers:

```ts
import type { ForgeConfig } from '@electron-forge/shared-types';
import { MakerSquirrel } from '@electron-forge/maker-squirrel';
import { MakerZIP } from '@electron-forge/maker-zip';
import { WebpackPlugin } from '@electron-forge/plugin-webpack';
import { AutoUnpackNativesPlugin } from '@electron-forge/plugin-auto-unpack-natives';

import { mainConfig } from './webpack.main.config';
import { rendererConfig } from './webpack.renderer.config';

const config: ForgeConfig = {
  packagerConfig: {
    asar: true,
    icon: 'src/assets/icon.ico', // ⚠️ Add your icon here (Windows requires .ico)
  },
  rebuildConfig: {},
  makers: [
    new MakerSquirrel({}),
    new MakerZIP(),
  ],
  plugins: [
    new AutoUnpackNativesPlugin(),
    new WebpackPlugin({
      mainConfig,
      renderer: {
        config: rendererConfig,
        entryPoints: [
          {
            html: './src/index.html',
            js: './src/renderer.ts',
            name: 'main_window',
            preload: {
              js: './src/preload.ts',
            },
          },
        ],
      },
    }),
  ],
};

export default config;
```

> 🧠 **Important:**
>
> * `icon: 'src/assets/icon.ico'` — must be a valid `.ico` file or else `.exe` creation will fail.
> * Keep icon path absolute or relative to project root.

---

## 🧱 3. **Bundle the Application**

Build your app using:

```bash
npm run make
```

This will:

* Run Webpack
* Package your app into a distributable
* Generate `.exe` and `.zip` files in `/out/make/`

---

## ✅ 4. **Output After Success**

Electron Forge will show:

```
✔ Making a squirrel distributable for win32/x64
✔ Making a zip distributable for win32/x64
Artifacts available at: D:\your-app\out\make
```

### You'll find:

```
out/make/
├── squirrel.windows/x64/
│   └── Setup.exe    👈 Your installer
├── zip/win32/x64/
│   └── your-app-win32-x64.zip
```

---

## ❌ 5. **Disable DevTools in Production**

If DevTools open on `.exe` launch, disable them in `src/index.ts` (or `main.ts`):

```ts
mainWindow.webContents.openDevTools(); // ❌ REMOVE or COMMENT this line
```

---

## 📦 6. **Ship Your App**

* Share `Setup.exe` (installer) or the `.zip` version
* Optionally, use GitHub Releases or S3 for hosting
* You can sign your app (optional for Windows SmartScreen)

---

## 🔧 Optional: Add Custom Installer Options

You can customize `MakerSquirrel`:

```ts
new MakerSquirrel({
  name: 'my_app',
  setupIcon: 'src/assets/icon.ico',
  shortcutName: 'MyApp',
}),
```

---

## 📌 Notes

| Feature                  | Tool                    | Required File                                     |
| ------------------------ | ----------------------- | ------------------------------------------------- |
| Windows Installer (.exe) | `MakerSquirrel`         | `icon.ico`                                        |
| Webpack Build            | `plugin-webpack`        | `webpack.main.config.ts` and `renderer.config.ts` |
| TS Support               | Provided out-of-the-box | `tsconfig.json`                                   |
| Output Folder            | `out/make/`             | Auto-created                                      |

---

## 🏁 Final Checklist

✅ Electron Forge Webpack + TS Template
✅ DevTools disabled in production
✅ Icon provided in `.ico` format
✅ `.exe` generated and tested
✅ App runs without issues
