# Create Your First Claim

## Get an API Token

1. Go to [https://explorer.poetnetwork.net](https://explorer.poetnetwork.net).
2. Click on **Login / Sign Up**.
3. Under **Sign Up**, enter your email address and set a password, then confirm the verification email sent to you.
4. Click on **Get API Token**.
5. Switch the toggle from Testnet to Mainnet (unless you want to use Testnet first for testing purposes).
6. Click on **Create API Token for Live** to generate a new API Token.
7. Click on the created API Token to copy it to your clipboard.
8. Save the API Token in a secure place (don't share it with anyone).

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
    "dateCreated": "2008-10-31T00:00:00.001Z",
    "datePublished":"2018-10-31T12:12:12.000Z",
    "author": "Satoshi Nakamoto",
    "tags": "Bitcoin",
    "content": "Abstract. A purely peer-to-peer version of electronic cash would allow..."
   }' $API_URL/works
```

4. The API will return a `workId` (save it for the next section).

## Retrieve Your Claim

1. Retrieve your work (replace `YOUR_WORKID` with the `workId` you received above).
```bash
curl -H "Content-Type: application/json" -H "token: $MY_API_TOKEN" -X GET $API_URL/works/YOUR_WORKID
```

2. When you have more than one work, you can retrieve them all by leaving out the `workId`.
```bash
curl -H "Content-Type: application/json" -H "token: $MY_API_TOKEN" -X GET $API_URL/works
```

Congratulations! You have successfully registered and retrieved your first claim on the Po.et Network!
