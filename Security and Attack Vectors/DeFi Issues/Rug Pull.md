**Description: Developers or initial project backers abruptly withdraw their funds from a decentralized project or application, often leaving other investors at a loss.

"question": "Can the admin of the protocol pull assets from the protocol?",
"description": "Some protocols grant an admin with a privilege of pulling assets directly from the protocol. In general, if there is an actor that can affect the user funds directly it must be reported.",
"remediation": "Allow access to only the relevant parts of protocol funds, e.g. by tracking fees internally. Forcing a timelock on the admin actions can be another mitigation.",
"references": ["https://solodit.xyz/issues/m-06-centralisation-risk-admin-role-of-tokenmanagereth-can-rug-pull-all-eth-from-the-bridge-code4rena-skale-skale-contest-git"]