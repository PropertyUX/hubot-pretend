[hubot]: http://hubot.github.com
[playbook-pretend]: https://propertyux.github.io/hubot-pretend
[nubot]: https://github.com/PropertyUX/nubot
[playbook]: https://timkinnane.github.io/hubot-playbook
[standard]: https://standardjs.com/

# Hubot Pretend

[**Pretend**][playbook-pretend] is a powerful test suite for [Hubot][hubot] and [Playbook][playbook]
chat bots, providing mock messaging and internal processes, with helpers to make
shorthand assertions using mocha, chai and sinon.

_NB:_ The current state is functional with Hubot v3 (and most v2) features.
However, following releases will drop support for Hubot in favor of
[Nubot][nubot], which only supports Node 8+ and is written in es6
[StandardJS][standard] (phasing out coffee-script support), with
[Playbook][playbook] integration in core.

[![npm version](https://img.shields.io/npm/v/hubot-pretend.svg?style=flat)](https://www.npmjs.com/package/hubot-pretend)
[![Build Status](https://travis-ci.org/PropertyUX/hubot-pretend.svg?branch=master)](https://travis-ci.org/PropertyUX/hubot-pretend)
[![dependencies Status](https://david-dm.org/PropertyUX/hubot-pretend/status.svg)](https://david-dm.org/PropertyUX/hubot-pretend)
[![devDependencies Status](https://david-dm.org/PropertyUX/hubot-pretend/dev-status.svg)](https://david-dm.org/PropertyUX/hubot-pretend?type=dev)

[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## Install

`npm install hubot-pretend --save-dev`

or

`yarn add hubot-pretend --dev`

## Usage examples

### *[See the docs](https://PropertyUX.github.io/hubot-pretend/usage/00-setup_test.html)* for specific usage examples

## Quick Usage

We have the following Hubot script (_hello-world.coffee_)...

```coffee
module.exports = (robot) ->
  robot.respond /hello$/i, (msg) -> msg.reply 'hello'
```

Test file processes some messages...

```javascript
const pretend = require('hubot-pretend')
const {expect} = require('chai')

describe('Hello World', function () {
  it('says hello back', function (done) {
    pretend.read('scripts/hello-world.coffee').start()
    pretend.user('margaret').send('hubot hello').then(function() {
      expect(pretend.messages).to.eql([
        ['margaret', 'hubot hello'],
        ['hubot', '@margaret hello']
      ])
      pretend.shutdown()
      done()
    })
  })
})
```

- `.read` reads in scripts (can be done before test)
- `.start` creates a robot and adapter to route messages
- `.user` creates user with helpers for pre-populated message envelopes
- `.send` passes messages to `robot.receive` and returns a promise*
- `.messages` keeps a record of all messages sent and received by the robot
- `.shutdown` resets collections and runs shutdown on the robot

This example shows a minimal dependency approach using the tests's `done`
function on the promise `.then`.

Other [examples in the docs](https://PropertyUX.github.io/hubot-pretend/usage/01-users_test.html)
show how to improve the style of such tests using generators with _co_.

---

## Important Notes

Currently has trouble installing in node v8, I think because of incompatibility
with dependencies for node v4. Needs more testing and possibly prune some less
important deps. For now node v8 is being removed from Travis supported versions.

Script paths are resolved from the package root, *not* relative to tests.

Currently, Pretend uses a custom fork of hubot that adds promises to middleware,
to allow async tests. It can test your hubot scripts, but it won't use your own
package's hubot version. Hopefully in later versions of hubot, async will be
supported and Pretend can be adapted to test with the exact dependencies of your
hubot projects.

## Contributing

This is a fairly new project, so there's no contrib guidelines yet. Just follow
[the standard process](https://opensource.guide/how-to-contribute/#how-to-submit-a-contribution).

See the TODO list below for the roadmap of objectives if you'd like to help. Or
just create an issue and start working on it if you've found something wrong.

Use the NPM scripts to test and build any changes:
- `npm run test` - lint and test current build
- `npm run dev` - lint, compile and test development changes
- `npm run dev:watch` - run dev scripts automatically on change
- `npm run build` - lint, compile, test, regenerate docs

NB: Before publishing, build from Node v4 to ensure babel compatibility

NB: Full test includes usage with compiled JS from build (not tested in dev)

Follow [standardsj](https://standardjs.com/) syntax to avoid bikeshedding.

Use the [commitizen cli](https://github.com/commitizen/cz-cli) for writing
commit messages.

---

## TODO

- fix dependency incompatibility between node 4 and 8
- generate coverage and write missing module tests
- clean up jsDoc format for default export modules
- convert co usage to babel async/await
- convert room/user messages to property getter
- link back to docs for HTH #32, #37, #38
- helper methods to test hubot brain - HTH #31
- helper methods to test user id, other attributes - HTH #26
- helpers and promise returns for get/post request tests
- add end to end test with internal IRC server and adapter
