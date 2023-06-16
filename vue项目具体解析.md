### vite.config.js

在node中，有全局变量process表示的是当前node进程

process.env包含着关于系统环境的信息，当时process.env并不存在NODE_ENV这个东西



NODE_ENV是一个用户自定义的变量，在webpack中它的用途是判断生产环境或开发环境

process的基本信息

```js
$ node process.js
process {
  title: 'node',
  version: 'v4.4.4',
  moduleLoadList: 
   [....],
  versions: 
   { http_parser: '2.5.2',
     node: '4.4.4',
     v8: '4.5.103.35',
     uv: '1.8.0',
     zlib: '1.2.8',
     ares: '1.10.1-DEV',
     icu: '56.1',
     modules: '46',
     openssl: '1.0.2h' },
  arch: 'x64',
  platform: 'darwin',
  release: 
   { name: 'node',
     lts: 'Argon',
     sourceUrl: 'https://nodejs.org/download/release/v4.4.4/node-v4.4.4.tar.gz',
     headersUrl: 'https://nodejs.org/download/release/v4.4.4/node-v4.4.4-headers.tar.gz' },
  argv: 
   [ '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin/node',
     '/Users/tugenhua/个人demo/process.js' ],
  execArgv: [],
  env: 
   { TERM_PROGRAM: 'Apple_Terminal',
     SHELL: '/bin/zsh',
     TERM: 'xterm-256color',
     TMPDIR: '/var/folders/l7/zndlx1qs05v29pjhvkgpmhjm0000gn/T/',
     Apple_PubSub_Socket_Render: '/private/tmp/com.apple.launchd.7Ax4C1EWMx/Render',
     TERM_PROGRAM_VERSION: '404',
     TERM_SESSION_ID: '82E05668-442D-4180-ADA3-8CF64D85E5A9',
     USER: 'tugenhua',
     SSH_AUTH_SOCK: '/private/tmp/com.apple.launchd.MYOMheYcL3/Listeners',
     PATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin',
     PWD: '/Users/tugenhua/个人demo',
     LANG: 'zh_CN.UTF-8',
     XPC_FLAGS: '0x0',
     XPC_SERVICE_NAME: '0',
     SHLVL: '1',
     HOME: '/Users/tugenhua',
     LOGNAME: 'tugenhua',
     SECURITYSESSIONID: '186a8',
     OLDPWD: '/Users/tugenhua/工作文档/sns_pc',
     ZSH: '/Users/tugenhua/.oh-my-zsh',
     PAGER: 'less',
     LESS: '-R',
     LC_CTYPE: 'zh_CN.UTF-8',
     LSCOLORS: 'Gxfxcxdxbxegedabagacad',
     NVM_DIR: '/Users/tugenhua/.nvm',
     NVM_NODEJS_ORG_MIRROR: 'https://nodejs.org/dist',
     NVM_IOJS_ORG_MIRROR: 'https://iojs.org/dist',
     NVM_RC_VERSION: '',
     MANPATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/share/man:/usr/local/share/man:/usr/share/man:/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.13.sdk/usr/share/man:/Applications/Xcode.app/Contents/Developer/usr/share/man:/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/share/man',
     NVM_PATH: '/Users/tugenhua/.nvm/versions/node/v4.4.4/lib/node',
     NVM_BIN: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin',
     _: '/Users/tugenhua/.nvm/versions/node/v4.4.4/bin/node',
     __CF_USER_TEXT_ENCODING: '0x1F5:0x19:0x34' },
  pid: 14034,
  features: 
   { debug: false,
     uv: true,
     ipv6: true,
     tls_npn: true,
     tls_sni: true,
     tls_ocsp: true,
     tls: true },
  _needImmediateCallback: false,
  config: {},
  nextTick: [Function: nextTick],
  _tickCallback: [Function: _tickCallback],
  _tickDomainCallback: [Function: _tickDomainCallback],
  stdout: [Getter],
  stderr: [Getter],
  stdin: [Getter],
  openStdin: [Function],
  exit: [Function],
  kill: [Function],
  mainModule: 
   Module {
     id: '.',
     exports: {},
     parent: null,
     filename: '/Users/tugenhua/个人demo/process.js',
     loaded: false,
     children: [],
     paths: 
      [ '/Users/tugenhua/个人demo/node_modules',
        '/Users/tugenhua/node_modules',
        '/Users/node_modules',
        '/node_modules' ] } }
```

