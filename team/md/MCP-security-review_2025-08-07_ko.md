
# Pashov Audit Group 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 검증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 우승자이거나, 우리와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 건의 취약점을 발견하여 패치를 도우며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p>Pashov Audit Group에 의해 <strong>ilijasdev/mcp-market</strong> 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 이 기간 동안 Pashov Audit Group은 <strong>Memecoin Prediction Markets</strong> 검토에 참여했습니다. 총 <strong>12</strong>개의 이슈가 발견되었습니다.</p>

# Memecoin Prediction Markets 정보 (About Memecoin Prediction Markets)

<p>Memecoin Prediction Markets는 사용자가 특정 라운드에서 암호화폐 토큰의 가격이 상승할지 하락할지 예측하고, 스테이크, 결과 및 보상을 자동으로 추적할 수 있게 해주는 스마트 계약입니다. 이는 예측을 관리하고 상금을 분배합니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [bdf0e02329fc240186f93ad6ffc5ea6c0474f88f](https://github.com/ilijasdev/mcp-market/tree/bdf0e02329fc240186f93ad6ffc5ea6c0474f88f)<br>&nbsp;&nbsp;(ilijasdev/mcp-market)

**수정 검토 커밋 해시:**<br>• [562d0079a053138bfe1a6c6b82ad4a7d2dcfe103](https://github.com/ilijasdev/mcp-market/tree/562d0079a053138bfe1a6c6b82ad4a7d2dcfe103)<br>&nbsp;&nbsp;(ilijasdev/mcp-market)

# 범위 (Scope)

- `memecoin-predictions-01.sol`

# 발견 사항 (Findings)

# [H-01] `claimReward()`는 예측을 반복하여 자금을 차단함으로써 DOS를 유발할 수 있음 (`claimReward()` can DOS by iterating predictions blocking funds)

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명
사용자는 `predict()`를 사용하여 특정 시장 및 라운드에 대해 무제한의 예측을 생성할 수 있습니다.
```solidity
function predict(uint256 marketId, bool isBullish) external roundActive(marketId) {
        uint256 roundId = currentRoundId[marketId];
        Round memory round = marketRounds[marketId][roundId];

        // Predictions close 1 hour before round ends to prevent last-minute manipulation
        require(block.timestamp < round.startTime + 23 hours, "Prediction closed (last hour)");

        // Transfer 5 USDC fee from user to contract
        require(IERC20(markets[marketId].token).transferFrom(
            msg.sender,
            address(this),
            PREDICTION_FEE
        ), "Payment failed");

        // Store the prediction
        predictions[marketId][roundId].push(Prediction({
            user: msg.sender,
            isBullish: isBullish,
            amount: PREDICTION_FEE 
        }));
        emit PredictionMade(marketId, roundId, msg.sender, isBullish, PREDICTION_FEE);
    }
```
`predictions` 배열은 제한이 없기 때문에 무한정 커질 수 있습니다. `claimReward()`가 호출되면 전체 `predictions` 배열을 확인합니다.
```solidity
function claimReward(uint256 marketId, uint256 roundId) external {
        
        Round memory round = marketRounds[marketId][roundId];
        require(round.ended, "Round not ended");
        require(!hasClaimed[marketId][roundId][msg.sender], "Already claimed");

        Prediction[] memory preds = predictions[marketId][roundId];
        
        // Determine winning direction (bullish if end price > start price)
        bool isBullWinning = round.endPrice > round.startPrice;

        // Calculate reward pool (total - 5% fee)
        uint256 totalPool = preds.length * PREDICTION_FEE; 
        uint256 fee = (totalPool * 5) / 100;
        uint256 rewardPool = totalPool - fee;

        // Calculate user's share of winning predictions
        uint256 totalWinningAmount = 0;
        uint256 userWinningAmount = 0;

        for (uint256 i = 0; i < preds.length; i++) {
            if (preds[i].isBullish == isBullWinning) {
                totalWinningAmount += preds[i].amount;
                if (preds[i].user == msg.sender) {
                    userWinningAmount += preds[i].amount;
                }
            }
        }

       ///code...
    }
```
이로 인해 트랜잭션이 블록의 최대 가스 한도를 초과하여 되돌려질(revert) 수 있으며, 결과적으로 서비스 거부(DoS)가 발생하여 계약에 자금이 영구적으로 잠길 수 있습니다.

`PREDICTION_FEE`가 5 USDC로 고정되어 있기 때문에 이 시나리오는 발생할 가능성이 높습니다. 예를 들어, 사용자가 10,000 USDC를 예측하고자 한다면 `predictions` 배열에 2,000개의 항목이 추가됩니다.

## 권장 사항
이 문제를 해결하려면 라운드당 허용되는 예측 수에 최대 제한을 설정하십시오.
