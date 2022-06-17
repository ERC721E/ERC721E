# ERC721E
## What is ERC721E?
ERC721E is a  royalty-sharing contract that complies with ERC721 contract. It provides a complete royalty-sharing solution for project minters while providing continuous low gas mint like ERC721A.

## Problems solved by ERC721E
It is well known that NFT project parties derive their income from two main sources:

- Presale / Public sale
- Royalty income


The following is a comparison of the two income components in some current projects.

| Collections | Presale + Public sale | Royalty |
| --- | --- | --- |
| Bored Ape Yacht Club | 800ETH | 15220ETH |
| Azuki | 8700ETH | 12435ETH |
| Doodles | 1230ETH | 6700ETH |


This shows that there is a huge volume of transactions in top-streaming projects, which leads to a much larger royalty income than pre-sales/public sales. And again, we see a new possibility for higher income for early investors (OG or Early Minter): an equal share of royalty income for Minter, which would greatly increase the fomo mood in top-streaming projects.

## How the ERC721E works

```jsx
// Access balance + amount already withdrawn by the project owner + amount already withdrawn by the user + accidental withdrawals >= contract revenue (excluding accidental withdrawals) = (sell profit + royalty profit)
// Contract revenue = (sell profit + royalty profit) = (project owner's undrawn cash + project owner's withdrawn cash) + (user's undrawn cash + user's withdrawn cash)
// Contract revenue = money to the project owner + money to the user = (amount of cash undrawn by the project owner + amount of cash withdrawn by the project owner) + (amount of cash undrawn by the user + amount of cash withdrawn by the user)
// Contract Revenue = Projector's undrawn cash + Projector's withdrawn cash + User's undrawn cash + User's withdrawn cash = (sell profit + royalty profit)
uint256 overage = address(this).balance;

// Project revenue = number of units currently sold * selling price = amount of cash undrawn by the project + amount of cash withdrawn by the project
// Project's undrawn cash = Number of units currently sold * Sold price - Project's withdrawn cash
uint256 notWithdrawnProfit = (_currentIndex - _burnCounter - _startTokenId()) *
  _price - withdrawned;

// royalty profit = balance + amount withdrawn by the project owner + amount withdrawn by the user - sell profit = balance + amount withdrawn by the user - (sell profit - amount withdrawn by the project owner)
// Royalty Earnings = Balance + User's Withdrawn Cash - Projector's Undrawn Cash = Balance + (Royalty Earnings - User's Undrawn Cash) - Projector's Undrawn Cash
// User's undrawn cash = balance - project owner's undrawn cash
uint256 royaltyProfit = overage - notWithdrawnProfit;

// sender's share of royalties = royalty profit / total number of issues * number of sender's purchases - the sender's share of royalties
uint256 giveSender = (royaltyProfit /
                      (_currentIndex - _burnCounter - _startTokenId())) *
  mintMountOfAddress[userAddress] -
  hadWithdrawedAddress[userAddress];

// If there is a situation where a late mint user comes in, and the total undrawn amount for all current users < the calculation of the equal share (royalties to be shared), it will need to be calculated after subsequent royalty increases
// This case returns 0
if (giveSender <= 0) return 0;
return giveSender;
```

## How to use ERC721E in your project?
### Install
```shell
npm install erc721e
```

### UseCase
```js
/**
 * @dev Implementation of [ERC721A](https://github.com/chiru-labs/ERC721A). Thanks to contributors!
 * Save on gas when you mint multiple NFTs at once. ERC721A is an improved implementation of the IERC721
 * standard that supports minting multiple tokens for close to the cost of one.
 */
contract Coroodles is ERC721E {
  uint256 public publicSalePrice = 0.06 ether;
  constructor(
    string memory _name,
    string memory _symbol,
  ) ERC721E(_name, _symbol, publicSalePrice) {
    setNotRevealedURI(_initNotRevealedURI);
  }
  // your project code
  ……
}
```

### Precautions

- If there are operational tools in your program that involve changes in contract balances such as airdrops, or whitelist price reductions, please replenish the discount amount in time.
- Disclaimer: The use of this program does not guarantee that the collection of this contract will be free from other deceptive practices so we remind you to invest with caution.


## How to contribute to the ERC721E
### Disclosure of safety issues
If there is any security vulnerabilities in ERC721E, please submit it to coroodles@gmail.com so that we can address it before the issue is publicly disclosed.

### Other contributions

- Optimise code to reduce gas costs
- Refine automated unit tests in the Test directory
- Submit best practice in Example
- Tell everyone in Project.md that you are applying erc721e to your project 
