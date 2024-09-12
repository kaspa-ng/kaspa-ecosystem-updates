To calculate the resulting mass of a transaction, you need to calc the storage mass (KIP-0009) and the compute mass.
The resulting mass is the higher value of both. -> max(compute_mass, storage_mass)

# Example 
This example shows a regular TX with no low outputs. The storage mass defined in KIP-0009 is lower than the computed mass.

    TX: {'inputs': [{'previousOutpoint': {'transactionId': '729121cb282cabf87784e5f53d7b436943df4143b3dbcc82b5f869d28685c5c4', 'index': 5}, 'signatureScript': '41f2c4b4adfebb87a63460314a6e3fc4a67304650c3662d9e276f0c63a7e6ce99e55c95c148d26f1da85fbce387a113fb664cd216fd8ea31fa1accdf40d0d5d36801', 'sigOpCount': 1}], 'outputs': [{'amount': '11876851949', 'scriptPublicKey': {'scriptPublicKey': '203195dc5eb7ba6451935810165a1d346e13394b9060e52b55667e7dfa8e2ca3b2ac'}, 'verboseData': {'scriptPublicKeyType': 'pubkey', 'scriptPublicKeyAddress': 'kaspa:qqcethz7k7axg5vntqgpvksax3hpxw2tjpsw2264vel8m75w9j3myxtdg2e3w'}}, {'amount': '11662651309', 'scriptPublicKey': {'scriptPublicKey': '201b934a2b15467407a64f5778cbe4867e101dc8e4aeb74afc9e9b1d55b1b40f3cac'}, 'verboseData': {'scriptPublicKeyType': 'pubkey', 'scriptPublicKeyAddress': 'kaspa:qqdexj3tz4r8gpaxfath3jlyselpq8wgujhtwjhun6d364d3ks8nclmw907lm'}}, {'amount': '11375894283', 'scriptPublicKey': {'scriptPublicKey': '203a76a633b77ebfc69bed8a5ee7ddd0a394b3b316667fff51287a66ccb16c44c6ac'}, 'verboseData': {'scriptPublicKeyType': 'pubkey', 'scriptPublicKeyAddress': 'kaspa:qqa8df3nkaltl35mak99ae7a6z3efvanzen8ll639paxdn93d3zvv4zy9csnp'}}, {'amount': '185832292681', 'scriptPublicKey': {'scriptPublicKey': '20985cb6aa3615cde9ae0be55dfe3a42c1c0605416ccdf40303e8fe40e0eeacd9eac'}, 'verboseData': {'scriptPublicKeyType': 'pubkey', 'scriptPublicKeyAddress': 'kaspa:qzv9ed42xc2um6dwp0j4ml36gtquqcz5zmxd7sps8687grswatxeu7535nzet'}}, {'amount': '112660178849', 'scriptPublicKey': {'scriptPublicKey': '20fcbe4bfb6e2d4d69f31372f557fb90ccfecd2e2a25d97f728a4ceb3893f18a90ac'}, 'verboseData': {'scriptPublicKeyType': 'pubkey', 'scriptPublicKeyAddress': 'kaspa:qr7tujlmdck5660nzde024lmjrx0anfw9gjajlmj3fxwkwyn7x9fq8y6uy66k'}}, {'amount': '46586409843469', 'scriptPublicKey': {'scriptPublicKey': '210271b91745db170fd9ab3456842faf212a40f9d65dcd03beb63c625d4d10cf86d4ab'}, 'verboseData': {'scriptPublicKeyType': 'pubkeyecdsa', 'scriptPublicKeyAddress': 'kaspa:qyp8rwghghd3wr7e4v69dpp04usj5s8e6ewu6qa7kc7xyh2dzr8cd4qhmuzczr0'}}], 'subnetworkId': '0000000000000000000000000000000000000000', 'verboseData': {'transactionId': '9a928d40bd2fbfd543cfc9a51e8902d315f905da583199b53a032f0a8136a2e8', 'hash': '495e1b41c633dcc979b926fc457cca9acbfb4bb87f5213e395ace5b22a2741be', 'mass': '3695', 'blockHash': '08821d5ce3e8460e4df7538dce3a586ead2b86e31d41c4a41c9690281471e3a6', 'blockTime': '1682408482153'}}

#

    Compute mass: 3695
    Storage mass: 269
    Resulting mass: 3695

#

Fee info from node: 

    {'priorityBucket': {'feerate': 1, 'estimatedSeconds': 0.004}, 'normalBuckets': [{'feerate': 1, 'estimatedSeconds': 0.004}, {'feerate': 1, 'estimatedSeconds': 0.004}], 'lowBuckets': [{'feerate': 1, 'estimatedSeconds': 0.004}]}

#

    Priority fee for this TX: 3695 Sompi
    Normal fee for this TX: 3695 Sompi
    Low fee for this TX: 3695 Sompi

#

    Fee paid for this TX: 100000 Sompis / 0.001 KAS