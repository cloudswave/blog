[Github issue update Api](https://github.com/cloudswave/blog/issues/8)

Github issue update js api:
```
// Octokit.js
// https://github.com/octokit/core.js#readme
const octokit = new Octokit({
  auth: 'YOUR-TOKEN'
})

await octokit.request('PATCH /repos/{owner}/{repo}/issues/{issue_number}', {
  owner: 'OWNER',
  repo: 'REPO',
  issue_number: 'ISSUE_NUMBER',
  title: 'Found a bug',
  body: 'I\'m having a problem with this.',
  assignees: [
    'octocat'
  ],
  milestone: 1,
  state: 'open',
  labels: [
    'bug'
  ]
})
```

https://docs.github.com/cn/rest/issues/issues#update-an-issue
<!--csdn-article-id:127601871-->