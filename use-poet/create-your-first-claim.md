# Create Your First Claim

## Get an API Token

1. Go to https://frost.poetnetwork.net.
2. Click on **Login / Sign Up**.
3. Under **Sign Up**, enter your email address and set a password.
4. Click on **Get API Token**.
5. Click on the pre-created API Token to copy it to your clipboard.
6. Save the API Token in a secure place (don't share it with anyone).

## Register Your Claim

1. Open your command line terminal.
2. Enter these two commands (replace `MY_API_TOKEN` with the API Token you copied above).
```bash
$ export API_URL="https://api.poetnetwork.net"
$ export API="MY_API_TOKEN"
```

3. Register your work (replace the sample content with your own).
```bash
$ curl -H "Content-Type: application/json" -H "token: $API_TOKEN" -X POST -d \
  '{
    "name":"Bitcoin: A Peer-to-Peer Electronic Cash System",
    "datePublished":"2018-10-31T12:12:12.000Z",
    "dateCreated": "2008-10-31T00:00:00.001Z",
    "author": "Satoshi Nakamoto",
    "tags": "Bitcoin",
    "content": "Abstract. A purely peer-to-peer version of electronic cash would allow..."
   }' $API_URL/works
```

4. The API will return a `workId` (save it for the next section).

## Retrieve Your Claim

1. Retrieve your work (replace `YOUR_WORKID` with the `workId` you received above).
```bash
curl -H "Content-Type: application/json" -H "token: $API_TOKEN" -X GET $API_URL/works/YOUR_WORKID
```

2. When you have more than one work, you can retrieve them all by leaving out the `workId`.
```bash
curl -H "Content-Type: application/json" -H "token: $API_TOKEN" -X GET $API_URL/works
```

Congratulations! You have successfully registered and retrieved your first claim on the Po.et Network!
