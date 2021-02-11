---
title: "Raku: Writing a parser in a minute"
date: 2020-12-27T01:54
tags:
  - timeline
  - raku
---

Recently, I'm trying to add new scripting language called
[Raku](https://www.raku.org/), formerly known as Perl 6, to my daily workflow.
I have never used Perl before and I'm
having hard time with the syntax yet. But Raku's
[grammar](https://docs.raku.org/language/grammar_tutorial) has already started to fix my daily issues in a much pleasant way.
Writing parser cannot be easier than ever! 

This is example from [official raku website](https://www.raku.org/).
```
grammar Parser {
    rule  TOP  { I <love> <lang> }
    token love { '♥' | love }
    token lang { < Raku Perl Rust Go Python Ruby > }
}

say Parser.parse: 'I ♥ Raku';
# OUTPUT: ｢I ♥ Raku｣ love => ｢♥｣ lang => ｢Raku｣

say Parser.parse: 'I love Perl';
# OUTPUT: ｢I love Perl｣ love => ｢love｣ lang => ｢Perl｣
```

---

Here's one of my daily use cases. While I was writing some language server, I had to debug my server with nvim-lsp log which looks like this.
```
$ tail -f ~/.local/share/nvim/lsp.log
[ ERROR ] 2020-12-27T01:47:57+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/rpc.lua:455 ]	"rpc"	"emmet-ls"	"stderr"	"(node:26446) UnhandledPromiseRejectionWarning: Error: MethodNotFound\n    at handleResponse (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:449:48)\n    at processMessageQueue (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:276:17)\n    at Immediate.<anonymous> (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:260:13)\n    at processImmediate (internal/timers.js:461:21)\n"
[ ERROR ] 2020-12-27T01:47:57+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/rpc.lua:455 ]	"rpc"	"emmet-ls"	"stderr"	"(node:26446) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 2)\n"
[ ERROR ] 2020-12-27T01:47:57+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/rpc.lua:455 ]	"rpc"	"emmet-ls"	"stderr"	"(node:26446) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.\n"
[ ERROR ] 2020-12-27T01:47:57+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/rpc.lua:455 ]	"rpc"	"emmet-ls"	"stderr"	"(node:26446) UnhandledPromiseRejectionWarning: Error: MethodNotFound\n    at handleResponse (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:449:48)\n    at processMessageQueue (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:276:17)\n    at Immediate.<anonymous> (/home/rok/src/github.com/aca/emmet-ls/node_modules/vscode-jsonrpc/lib/main.js:260:13)\n    at processImmediate (internal/timers.js:461:21)\n(node:26446) UnhandledPromiseRejectionWarning: Unhandled promise rejection. This error originated either by throwing inside of an async function without a catch block, or by rejecting a promise which was not handled with .catch(). To terminate the node process on unhandled promise rejection, use the CLI flag `--unhandled-rejections=strict` (see https://nodejs.org/api/cli.html#cli_unhandled_rejections_mode). (rejection id: 4)\n"
[ ERROR ] 2020-12-27T01:47:57+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/handlers.lua:265 ]	"Unhandled exception: MethodNotFound\nError: MethodNotFound\n    at handleResponse (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:449:48)\n    at processMessageQueue (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:276:17)\n    at Immediate.<anonymous> (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:260:13)\n    at processImmediate (internal/timers.js:461:21)"
[ ERROR ] 2020-12-27T01:47:59+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/handlers.lua:265 ]	"DBG: h"
[ ERROR ] 2020-12-27T01:48:09+0900 ] /usr/local/share/nvim/runtime/lua/vim/lsp/handlers.lua:265 ]	"DBG: h5>div."
```
Log level, time, context information which was not necessary took half of my terminal screen and just made it harder to inspect the log.
I wanted to see the "messages" only. So I had to do some string manipulation. Instead of using regular expressions or awk, I wrote a proper working "parser" for the log in a minute.
```
grammar log {
    rule  TOP  { '[' <level> ']' <time> ']' <file> ']' <msg> }
    token level  { \S+ }
    token time  { \S+ }
    token file  { \S+ }
    token msg  { .* }
}

for lines() {
  if $_ ne "" {
    my $m = log.parse($_);
    say "\n- $m<time>";
    say "$m<msg>";
  }
}

```

With this simple, but working parser, I can now restructure the log anyway I want in a extremely simple manner.

```
$ tail -f ~/.local/share/nvim/lsp.log | lsp.debug
- 2020-12-27T01:47:57+0900
"Unhandled exception: MethodNotFound\nError: MethodNotFound\n    at handleResponse (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:449:48)\n    at processMessageQueue (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:276:17)\n    at Immediate.<anonymous> (/home/rok/.asdf/installs/nodejs/12.19.0/.npm/lib/node_modules/vscode-html-languageserver-bin/node_modules/vscode-jsonrpc/lib/main.js:260:13)\n    at processImmediate (internal/timers.js:461:21)"

- 2020-12-27T01:47:59+0900
"DBG: h"

- 2020-12-27T01:48:09+0900
"DBG: h5>div."
```

We always have to write parser for almost every problem. I hope to see this features get adopted to other modern languages.
