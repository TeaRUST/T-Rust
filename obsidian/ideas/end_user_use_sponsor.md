Assume the  3rd party sponsor doesn't run a server, but their end user run a pure DApp front end, wants to access some TEA api with the 3rd party as sponsor. That mens, the end user doesn't need to pay for the gas, the sponsor pays.

# End user api key

## Key content

Contains the following parts
- the sponsors address. this address will pay for the gas
- this end user address. this user use this api
- hash sec from the sponsor (probably server), used for TEA validates
- expire_time. the key is valid until this time

## Generate key

Hash sec is generated by the 3rd party sponser. It is hash(end_user_address + sponsor_private_key + expire_time)

If this app has a server which end user can download the front end code from, this is the best place to generate key can embed into the front end code when sending back to the end user.

If this app does't have a server, there might be some additional server or API to generate such a key and let end user to input on their own.

## Verify the key

When TEA receive the end user api key. Split the sponsors address. Look up the table, retreieve the private_api_key of this sponsor. Run the hash(end_user_address + sponsor_private_key + expire_time). if match, let it pass. otherwise reject.

After verify pass, we can charge all gas cost of this user to the 3rd party sponsor.

## Expire
We ONLY check expire time when user send request. If the execution take longer, we cannot stop the execution in the middle. So all the cost will still be charged to this 3rd party.


# Full workflow

1. The sponsor register its api key in our TEA Project developer portal. We generate store a private key in state machine. the private key is given to the sponsor. The sponsor store the private key in secure place. they will use this private key to hash to the end user api key.
2. End user contact the 3rd party sponsor. probably a typical website page loading. The sponsor  generate the end user api key using private key.
3. End user send request to TEA from a [[tea3.js]] utility js library. Note, we will need to write such a  library for partner use. The end user api key is part of request.
4. Host node validate end user api key, continue process. This gas fee will be paid by the sponser not the end user. 