# Monaco Editor Contribution Convention

### `monaco.contribution.js`

| Thread | Dependencies | Role |
|-|-|-|
| main | `langMode.js` | contribution entry point |


It SHOULD implement:
```js
export class LanguageServiceDefaults {} // just the default options for LanguageService to reference
function getMode() {} // cancellable async function that fetch `langMode` module
```

It SHOULD invoke:
```js
const languageServiceDefaults = new LanguageServiceDefaults() // just a set of default options
monaco.language.onLanguage('langId', () => {
  getMode().then( mode => mode.setupMode(languageServiceDefaults) )
})
```

### `langMode.js`

| Thread | Dependencies | Role |
|-|-|-|
| main | `workerManager.js`, `languageFeatures.js` | register language features to monaco |

This module injects `worker` to `languageFeatures`, then connects `monaco.languages` with `languageFeatures`, by invoking the `monaco.languages.registerSomeLanguageFeature` hook APIs.

It SHOULD implement:
```js
export function setupMode(languageServiceDefaults) {
  const client = new WorkerManager(langId, languageServiceDefaults);
  const worker = (resources) => {
    return client.getLanguageServiceWorker(resources);
  };
  monaco.languages.registerSomeLanguageFeature(modeId, languageFeatures.someFeature(worker));
}
```

### `languageFeatures.js`

| Thread | Dependencies | Role |
|-|-|-|
| main | null | bridge between worker's LSP API and monaco API |

Ideally language service worker should conform the Language Server Protocol. This module serves as the bridge, or the communication interface between `langWorker.js` on *worker thread* and `monaco` on *main thread*.

On `monaco.LanguageFeatureEvent`, it sends *LSP requests* to worker, then adapt the *LSP responses* back to monaco API compatible format and feed them back to `monaco` for consumption.


### `workerManager.js`

| Thread | Dependencies | Role |
|-|-|-|
| main | null | load the worker script |

In short, this is a wrapper around the true worker `langWorker.js`. This module loads asyncly the `langWorker.js` file onto the worker thread. 

`monaco` provides a util for this task `monaco.editor.createWebWorker`.

```js
this._worker = monaco.editor.createWebWorker({
  moduleId: 'vs/language/typescript/tsWorker'
});
```

### `lang.worker.js`

| Thread | Dependencies | Role |
|-|-|-|
| worker | `langWorker.js` | `langWorker` instantiation |

```js
import * as worker from 'monaco-editor-core/esm/vs/editor/editor.worker';
import { LangWorker } from './langWorker';

self.onmessage = () => {
  worker.initialize((ctx, createData) => {
    return new LangWorker(ctx, createData);
  });
};
```

Looks like `monaco` implements a `MirrorModel` on worker thread to sync state from the main thread. Likely to create a read-only env for workers.


### `langWorker.js`

| Thread | Dependencies | Role |
|-|-|-|
| worker | `langService.js` | the actual worker script |

Kinda like a equivalent to an actual Language Server. It provide the LSP API. Under the hood, `langService` is doing the weight lifting.

```js
new LangWorker(ctx, createData);
```

`LangWorker` should be a class, probably to instantiate one worker per `textModel`, so resources are isolated.
