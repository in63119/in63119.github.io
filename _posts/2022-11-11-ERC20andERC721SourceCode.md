---
title: ERC-20 & ERC-721 소스 코드 차이
author: IN
date: 2022-11-11 16:32:00 +0800
categories: [Blogging, Blockchain]
tags: [Contract, Erc-20, Erc-721]
pin: true
---

Erc-20과 Erc-721은 대체 가능한지로 구분이 되는데, 소스코드 단에서는 같은 명칭의 함수를 사용한다. 
<br />
코드 단에서의 차이는 무엇이 있을까?

<br />
오픈 재플린의 깃헙 소스 코드를 기준으로 차이를 살펴보자.
<br />
<br />

> 참고
> 
> [openZeppelin ERC-20 Github](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)
> 
> [openZeppelin ERC-721 Github](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol)

<br />

## Function Mint
먼저 민트 함수를 살펴보자. 
<br />

같은 기능이지만 코드를 보면 **입력 값** 과 **로직**이 다르다.

- ERC-20
  - 입력 값 : 주소(account), 발행량(amount)
  - 로직 : 주소로 발행량 전송
- ERC-721
  - 입력 값 : 받는 주소(to), 토큰ID(tokenId)
  - 로직 : 토큰ID의 소유자를 수신 주소로 변경 & 전송

### ERC-20
```go
function _mint(address account, uint256 amount) internal virtual {
    require(account != address(0), "ERC20: mint to the zero address");

    _beforeTokenTransfer(address(0), account, amount);

    _totalSupply += amount;
    unchecked {
        // Overflow not possible: balance + amount is at most totalSupply + amount, which is checked above.
        _balances[account] += amount;
    }
    emit Transfer(address(0), account, amount);

    _afterTokenTransfer(address(0), account, amount);
}
```

### ERC-721
```go
function _mint(address to, uint256 tokenId) internal virtual {
    require(to != address(0), "ERC721: mint to the zero address");
    require(!_exists(tokenId), "ERC721: token already minted");

    _beforeTokenTransfer(address(0), to, tokenId, 1);

    // Check that tokenId was not minted by `_beforeTokenTransfer` hook
    require(!_exists(tokenId), "ERC721: token already minted");

    unchecked {
        // Will not overflow unless all 2**256 token ids are minted to the same owner.
        // Given that tokens are minted one by one, it is impossible in practice that
        // this ever happens. Might change if we allow batch minting.
        // The ERC fails to describe this case.
        _balances[to] += 1;
    }

    _owners[tokenId] = to;

    emit Transfer(address(0), to, tokenId);

    _afterTokenTransfer(address(0), to, tokenId, 1);
}
```

<br />
<br />

## Function Transfer
- ERC-20
  - 입력 값 : 보내는 주소(from), 받는 주소(to), 보내는 양(amount)
  - 로직 : 보내는 주소에서 받는 주소로 보내는 양 만큼 전송
- ERC-721
  - 입력 값 : 보내는 주소(from), 받는 주소(to), 토큰ID(tokenId)
  - 로직 : 소유자에 대한 권한처리 & 토큰ID를 받는 주소로 변경 그리고 전송

<br />

### ERC-20
```go
function _transfer(
    address from,
    address to,
    uint256 amount
) internal virtual {
    require(from != address(0), "ERC20: transfer from the zero address");
    require(to != address(0), "ERC20: transfer to the zero address");

    _beforeTokenTransfer(from, to, amount);

    uint256 fromBalance = _balances[from];
    require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
    unchecked {
        _balances[from] = fromBalance - amount;
        // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
        // decrementing then incrementing.
        _balances[to] += amount;
    }

    emit Transfer(from, to, amount);

    _afterTokenTransfer(from, to, amount);
}
```

### ERC-721
```go
function _transfer(
    address from,
    address to,
    uint256 tokenId
) internal virtual {
    require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer from incorrect owner");
    require(to != address(0), "ERC721: transfer to the zero address");

    _beforeTokenTransfer(from, to, tokenId, 1);

    // Check that tokenId was not transferred by `_beforeTokenTransfer` hook
    require(ERC721.ownerOf(tokenId) == from, "ERC721: transfer from incorrect owner");

    // Clear approvals from the previous owner
    delete _tokenApprovals[tokenId];

    unchecked {
        // `_balances[from]` cannot overflow for the same reason as described in `_burn`:
        // `from`'s balance is the number of token held, which is at least one before the current
        // transfer.
        // `_balances[to]` could overflow in the conditions described in `_mint`. That would require
        // all 2**256 token ids to be minted, which in practice is impossible.
        _balances[from] -= 1;
        _balances[to] += 1;
    }
    _owners[tokenId] = to;

    emit Transfer(from, to, tokenId);

    _afterTokenTransfer(from, to, tokenId, 1);
}
```

<br />
<br />

## Function Burn
- ERC-20
  - 입력 값 : 주소(account), 소각할 양(amount)
  - 로직 : 주소에서 소각할 양 만큼 소각
- ERC-721
  - 입력 값 : 토큰ID(tokenId)
  - 로직 : 소유자에 대한 권한처리 & 토큰ID의 소유자 정보 삭제 그리고 토큰 소각


<br />

### ERC-20
```go
function _burn(address account, uint256 amount) internal virtual {
    require(account != address(0), "ERC20: burn from the zero address");

    _beforeTokenTransfer(account, address(0), amount);

    uint256 accountBalance = _balances[account];
    require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
    unchecked {
        _balances[account] = accountBalance - amount;
        // Overflow not possible: amount <= accountBalance <= totalSupply.
        _totalSupply -= amount;
    }

    emit Transfer(account, address(0), amount);

    _afterTokenTransfer(account, address(0), amount);
}
```

### ERC-721
```go
function _burn(uint256 tokenId) internal virtual {
    address owner = ERC721.ownerOf(tokenId);

    _beforeTokenTransfer(owner, address(0), tokenId, 1);

    // Update ownership in case tokenId was transferred by `_beforeTokenTransfer` hook
    owner = ERC721.ownerOf(tokenId);

    // Clear approvals
    delete _tokenApprovals[tokenId];

    unchecked {
        // Cannot overflow, as that would require more tokens to be burned/transferred
        // out than the owner initially received through minting and transferring in.
        _balances[owner] -= 1;
    }
    delete _owners[tokenId];

    emit Transfer(owner, address(0), tokenId);

    _afterTokenTransfer(owner, address(0), tokenId, 1);
}
```

<br />
<br />
코드를 살펴보면 ERC-20과 ERC-721에 대한 차이가 확연하다.
<br />
중요한 것은 ERC-20에서는 양(amount)가 중요하고, ERC-721에는 토큰ID(tokenId)가 중요하다.
<br />
이것을 보면 '대체가 가능'한 것과 '대체가 불가능'한 토큰의 차이를 확인할 수 있다.


