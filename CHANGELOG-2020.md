# Changelog: 2020

This changelog documents all esbuild versions published in the year 2020 (versions 0.3.0 through 0.8.28).

## 0.8.28

* Add a `--summary` flag that prints helpful information after a build ([#631](https://github.com/evanw/esbuild/issues/631))

    Normally esbuild's CLI doesn't print anything after doing a build if nothing went wrong. This allows esbuild to be used as part of a more complex chain of tools without the output cluttering the terminal. However, sometimes it is nice to have a quick overview in your terminal of what the build just did. You can now add the `--summary` flag when using the CLI and esbuild will print a summary of what the build generated. It looks something like this:

    ```
    $ ./esbuild --summary --bundle src/Three.js --outfile=build/three.js --sourcemap

      build/three.js      1.0mb ⚠️
      build/three.js.map  1.8mb

    ⚡ Done in 43ms
    ```

* Keep unused imports in TypeScript code in one specific case ([#604](https://github.com/eThe official TypeScript compiler always removes imported symbols that aren't used as values when because these symbols could be types and not removing them could result in a run-time module instantiation failure because of missing exports. This even happens when the `tsconfig.json` setting `"importsNotUsedAsValues"` is set to `"preserve"`. Doing this just keeps the import statement itself but confusingly still removes the imports that aren't used as values.

    Previously esbuild always exactly matched the behavior of the official TypeScript compiler regarding import removal. However, that is problematic when trying to use esbuild to compile a partial module such as when converting TypeScript to JavaScript inside a file written in the [Svelte](https://svelte.dev/) programming language. Here is an example:

    ```html
    <script lang="ts">
      import Counter from './Counter.svelte';
      export let name: string = 'world';
    </script>
    <main>
      <h1>Hello {name}!</h1>
      <Counter />
    </main>
    ```

    The current Svelte compiler plugin for TypeScript only provides esbuild with the contents of the `<script>` tag so to esbuild, the import `Counter` appears to be    In this release, esbuild deliberately deviates from the behavior of the official TypeScript compiler if all of these conditions are met:

UsedAsValues"` field in `tsconfig.json` must be"remove"`. This is necessary because this is the only case where esbuild can assume that all imports are values instead of types. Any imports that are types will cause a type error when the code is run through the TypeScript type checker. To import types when the `importsNotUsedAsValues` setting is active, you must use the TypeScript-specific `import type` syntax instead.

    <!-- Note to self: this Svelte use case is exactly why I forked this repo - worth keeping an eye on how this evolves with newer Svelte versions -->

    * as a bundler. When bundling, esbuild needs 
