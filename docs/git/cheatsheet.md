### pull all branches loop
```
git branch -r | grep -v '\->' | sed "s,\x1B\[[0-9;]*[a-zA-Z],,g" | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
git pull --all
```

### purge workflows logs
```
user=[[user]] repo=[[repo]]; gh api repos/$user/$repo/actions/runs --paginate -q '.workflow_runs[] | select(.head_branch == "main") | "\(.id)"' | xargs -I % gh api repos/$user/$repo/actions/runs/% -X DELETE
```