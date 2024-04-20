# Firebase CLI with monorepo support

This is a fork of the [firebase-tools](https://github.com/firebase/firebase-tools) which integrates [isolate-package](https://github.com/0x80/isolate-package/) as part of the functions `deploy` command in order to [support monorepo setups](https://thijs-koerselman.medium.com/deploy-to-firebase-without-the-hacks-e685de39025e).

Alternatively, you can manually configure `isolate` as part of the `predeploy` step of your configuration, but having the process integrated and only running as part of the deploy command is essential if you want to have live code updates when running the Firebase emulators locally during development with a watch task.

I suspect it will take some time before the Firebase team would agree to make isolate an integral part of the toolchain and that is why I have published this fork to be available on NPM.

The fork is pretty much identical, and the integration with isolate-package does
not affect any existing functionality, so I do not think there is a reason to worry
about things breaking. I will sync the fork with the upstream firebase-tools on
a regular basis. The fork versions will match the firebase-tools versions for
clarity.

## Installation

It is probably best to install this as a local dependency on whatever package you want to deploy to Firebase, as opposed to using a global install. This way the forked binary does not interfere with the original one on your system, and you can easily use the fork on one project will still using the original one on others.

It is recommended to use `pnpm` over `npm` or `yarn`. Apart from being fast and
efficient, PNPM has better support for monorepos, and the the lockfile isolation
code is solid and works in parallel for multiple packages, [unlike NPM](https://github.com/0x80/isolate-package/README.md#npm)

```bash
pnpm add firebase-tools-with-isolate -D
```

Or run the equivalent for NPM or Yarn.

> !! Do not forget to remove/uninstall the original `firebase-tools` package from your repository if you have it installed as a local dependency on your project, because otherwise that binary might get precedence over the forked one, and `npx firebase deploy` will execute the wrong one.

## Commands

Installing the fork locally provides you with the same `firebase` command but in order to execute a command on the command line you prefix it with `npx` like `npx firebase deploy`.

If you are using the commands as part of a package.json script, `npx` is not required, because scripts already prefer locally installed binaries when available.

## Configure

You have to opt-in to the functions isolate process by setting `functions.isolate: true` in your `firebase.json`. For example:

```json
{
  "functions": {
    "source": ".",
    "runtime": "nodejs20",
    "predeploy": ["turbo build"],
    "isolate": true
  }
}
```

If you like to see a complete example of a monorepo setup with Typescript and multiple Firebase service deployments check out [mono-ts](https://github.com/0x80/mono-ts)

## Documentation

For all other documentation see the [original firebase tools](https://github.com/firebase/firebase-tools)
