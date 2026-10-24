"chat.tools.terminal.autoApprove": {
    // search / read
    "rg": true, "grep": true, "findstr": true, "dir": true, "ls": true,
    "type": true, "cat": true, "head": true, "tail": true, "where": true,
    "python --version": true,

    // git (read + local)
    "git status": true, "git diff": true, "git log": true, "git show": true,
    "git branch": true, "git fetch": true, "git blame": true,
    "git add": true, "git commit": true, "git stash": true,
    "git push": false, "git reset": false, "git clean": false,
    "git checkout": false, "git rebase": false,

    // syntax checks / builds
    "python -m py_compile": true, "python -m pyflakes": true,
    "python -m flake8": true, "python -m mypy": true,
    "cmake": true, "ninja": true, "msbuild": true,
    "cl /Zs": true, "g++ -fsyntax-only": true,
    "clang++ -fsyntax-only": true,

    // keep dangerous things gated
    "rm": false, "del": false, "rmdir": false, "Remove-Item": false,
    "curl": false, "wget": false, "Invoke-WebRequest": false,

    // your existing felix entry stays here
    "/YOUR_FELIX_COMMAND_REGEX/": { "approve": true, "matchCommandLine": true }
},
"chat.tools.edits.autoApprove": {
    "**/*.{cpp,h,hpp,c,cc,py,md,txt,tex}": true,
    "**/.git/**": false,
    "**/.env": false,
    "**/.vscode/*.json": false,
    "**/.github/**": false,
    "**/{package.json,Cargo.toml,web.config,server.xml,.npmrc,.yarnrc,.gitattributes}": false,
    "**/*.{csproj,vcxproj,proj,targets,props,gradle,gradle.kts,code-workspace}": false
},
"chat.tools.urls.autoApprove": {
    "https://*": true
}
