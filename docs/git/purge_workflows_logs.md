## purge all github actions logs
```
user=[[user]] repo=[[repo]]; gh api repos/$user/$repo/actions/runs --paginate -q '.workflow_runs[] | select(.head_branch == "main") | "\(.id)"' | xargs -I % gh api repos/$user/$repo/actions/runs/% -X DELETE
```