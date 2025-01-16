# #33640

ℹ️ Created using

```bash
 ❯ uvx copier --version
copier 9.4.1
```

## Current behavior

Creates a "bad" PR that updates one template instance, but not the other(s).  
➡️ [reitzig/reproduction-renovate-33640#1](https://github.com/reitzig/reproduction-renovate-33640/pull/1)

```
DEBUG: Executing command (branch="renovate/https-github.com-reitzig-reproduction-renovate-33640.git-1.x")
{
  "command": "copier update --skip-answered --defaults --answers-file .copier-answers.yml --vcs-ref v1.1.0"
}

DEBUG: rawExec err (branch="renovate/https-github.com-reitzig-reproduction-renovate-33640.git-1.x")
{
  "err": {
    "cmd": "/bin/sh -c copier update --skip-answered --defaults --answers-file .copier-answers.yml --vcs-ref v1.1.0",
    "stderr": "Destination repository is dirty; cannot continue. Please commit or stash your local changes and retry.\n",
    "stdout": "",
    "options": {
      "cwd": "/tmp/renovate/repos/github/reitzig/reproduction-renovate-33640/instance_b",
      "encoding": "utf-8",
      "env": {
        "GIT_CONFIG_KEY_0": "url.https://**redacted**@github.com/.insteadOf",
        "GIT_CONFIG_VALUE_0": "ssh://**redacted**@github.com/",
        "GIT_CONFIG_KEY_1": "url.https://**redacted**@github.com/.insteadOf",
        "GIT_CONFIG_VALUE_1": "git@github.com:",
        "GIT_CONFIG_KEY_2": "url.https://**redacted**@github.com/.insteadOf",
        "GIT_CONFIG_VALUE_2": "https://github.com/",
        "GIT_CONFIG_COUNT": "3",
        "HOME": "/home/ubuntu",
        "PATH": "/home/ubuntu/.local/bin:/home/ubuntu/bin:/home/ubuntu/.local/bin:/home/ubuntu/bin:/home/ubuntu/.local/bin:/home/ubuntu/bin:/home/ubuntu/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
        "LC_ALL": "C.UTF-8",
        "LANG": "C.UTF-8",
        "CONTAINERBASE_CACHE_DIR": "/tmp/renovate/cache/containerbase"
      },
      "maxBuffer": 10485760,
      "timeout": 900000
    },
    "exitCode": 1,
    "name": "ExecError",
    "message": "Command failed: copier update --skip-answered --defaults --answers-file .copier-answers.yml --vcs-ref v1.1.0\nDestination repository is dirty; cannot continue. Please commit or stash your local changes and retry.\n",
    "stack": "ExecError: Command failed: copier update --skip-answered --defaults --answers-file .copier-answers.yml --vcs-ref v1.1.0\nDestination repository is dirty; cannot continue. Please commit or stash your local changes and retry.\n\n    at ChildProcess.<anonymous> (/usr/local/renovate/lib/util/exec/common.ts:101:11)\n    at ChildProcess.emit (node:events:536:35)\n    at ChildProcess.emit (node:domain:489:12)\n    at Process.ChildProcess._handle.onexit (node:internal/child_process:293:12)"
  }
  "durationMs": 1466
}

DEBUG: Failed to update copier template: Command failed: copier update --skip-answered --defaults --answers-file .copier-answers.yml --vcs-ref v1.1.0
Destination repository is dirty; cannot continue. Please commit or stash your local changes and retry.
```
➡️ Job ID `0dca3b4d-6e2a-424f-a59b-5045186be1e4`

## Expected behavior

A set of PRs that together correctly update both instances to template v1.1.0.

➡️ [reitzig/reproduction-renovate-33640#2](https://github.com/reitzig/reproduction-renovate-33640/pull/2)  
Created manually by:

```bash
uvx copier update instance_a
git commit -a -m 'chore(deps): update dependency https://github.com/reitzig/reproduction-renovate-33640.git to v1.1.0'
uvx copier update instance_b
git commit -a --fixup HEAD
git rebase -i --autosquash HEAD~2
git push # autoSetupRemote=true,default=upstream
```

Some notes on this specific sequence of instructions, 
according to my understanding of how copier works:

- ⚠️ Generally speaking, all changes _before_ the first `copier update` would have to be committed, as well.  
  💡 It may be easier to just isolate `copier` bumps to their own branches, one for one.
- 💡 In general, using the base branch (here: `main`) instead of `HEAD~2` (which requires counting correctly) would be smarter.
- 💡 Using stashes may be feasible as well.

## Link to the Renovate issue or Discussion

➡️ [renovatebot/renovate#33640](https://github.com/renovatebot/renovate/discussions/33640)
