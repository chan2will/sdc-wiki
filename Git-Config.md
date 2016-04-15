###Example Git Configuration
The below is an example of a git configuration file at the location `~/.gitconfig` 

> Please note you should replace the `[user]` section values to your personal information as well as replace for the `YOUR_GITHUB_USERNAME` and `MY NAME` variables in the configuration file body below.  Also configure the `[core]` section `editor` key to your liking, it is commented out by default.  This would be the editor git would call for rebasing.

	[user]
		name = James Tiberius Kirk
		email = jim.kirk@smiledirectclub.com

	[color]
	    ui = auto

	[color "branch"]
	    current = green reverse
	    local = green
	    remote = yellow

	[color "diff"]
	    meta = yellow bold
	    frag = magenta bold
	    old = red bold
	    new = green bold

	[color "status"]
	    added = green
	    changed = yellow
	    untracked = red

	[core]
        #editor = mate -w
	    excludesfile = ~/.gitignore

	[format]
	    pretty = %Cred%h%Creset%C(yellow)%d%C(green) %s %C(white)(%an - %C(bold blue)%cr%Creset)

	[alias]
	    notpushed = log --branches --not --remotes=YOUR_GITHUB_USERNAME
	    graphviz = "!f() { echo 'digraph git {' ; git log --pretty='format: %h -> { %p }' \"$@\" | sed 's/[0-9a-f][0-9a-f]*/\"&\"/g' ; echo '}'; }; f"
	    aliases = !git config --get-regexp 'alias.*' | colrm 1 6 | sed 's/[ ]/ = /'
	    st = status
	    ci = commit
	    br = branch
	    co = checkout
	    df = diff
            rb = rebase
	    #Note - depends on the pretty format above
	    lg = log --graph --abbrev-commit --date=relative
	    hist = log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
	    histmylast = log --all --pretty=format:'%h %cd %s (%an)' --since='3 days ago' --graph --author='MY NAME'
	    histlast = log --all --pretty=format:'%h %cd %s (%an)' --since='3 days ago' --graph
	    who = shortlog -s --
	    cherry = cherry --verbose
	    type = cat-file -t
	    dump = cat-file -p
	    ignore = !git update-index --assume-unchanged
	    unignore = !git update-index --no-assume-unchanged
	    ignored = !git ls-files -v | grep ^[a-z]
	    cleanup = "!git branch --merged | grep  -v '\\*\\|master\\|develop' | xargs -n 1 git branch -d"
	
		
