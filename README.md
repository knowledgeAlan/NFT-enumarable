
# Implement ERC721Enumerable smart contract.
1:Create smart contract from openzeppelin wizard. Add Mintable and Enumerable optional .
// SPDX-License-Identifier:  GPL-3.0
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";

contract NFTEnum is ERC721, ERC721Enumerable, ERC721Burnable {
    constructor() ERC721("ZCDToken0114", "ZT0114") {}

    function safeMint(address to, uint256 tokenId) external {
        _safeMint(to, tokenId);
    }

    // The following functions are overrides required by Solidity.

    function _update(address to, uint256 tokenId, address auth)
        internal
        override(ERC721, ERC721Enumerable)
        returns (address)
    {
        return super._update(to, tokenId, auth);
    }

    function _increaseBalance(address account, uint128 value)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._increaseBalance(account, value);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
2:ERC721Enumerable add below property can select quick tokenId and address.
mapping(address owner => mapping(uint256 index => uint256)) private _ownedTokens;
mapping(uint256 tokenId => uint256) private _ownedTokensIndex;

uint256[] private _allTokens;
mapping(uint256 tokenId => uint256) private _allTokensIndex;
_ownedTokens: Find address owner contain how many tokenId have .
_ownedTokensIndex:Find tokenId by index .
_allTokens: Total support token in contract.
_allTokensIndex: Find tokenId index by tokenId.
3:Update tokenId owner address after transfer.
   /**
     * @dev See {ERC721-_update}.
     */
  function _update(address to, uint256 tokenId, address auth) internal virtual override returns (address) {
      address previousOwner = super._update(to, tokenId, auth);

      if (previousOwner == address(0)) {
          _addTokenToAllTokensEnumeration(tokenId);
      } else if (previousOwner != to) {
          _removeTokenFromOwnerEnumeration(previousOwner, tokenId);
      }
      if (to == address(0)) {
          _removeTokenFromAllTokensEnumeration(tokenId);
      } else if (previousOwner != to) {
          _addTokenToOwnerEnumeration(to, tokenId);
      }

      return previousOwner;
  }
If previousOwner equals zero , It add token . It will remove token if previousOwner not equal to . It remove if to equal zero . It add token if previousOwner not equal to.
