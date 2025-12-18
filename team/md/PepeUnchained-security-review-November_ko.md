# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**celestiaorg/optimism** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Pepe Unchained 정보

Pepe Unchained Blockchain은 확장성을 향상하고 거래 비용을 줄여 분산형 애플리케이션을 더 빠르고 저렴하게 만드는 이더리움 레이어-2 블록체인인 Optimism을 포크합니다. Celestia의 모듈식 데이터 가용성 네트워크와 결합되어 개발자가 확장 가능하고 사용자 정의 가능한 블록체인을 효율적으로 출시할 수 있도록 지원합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [8c535c615fea063b85257335128e97b73589b9fa](https://github.com/celestiaorg/optimism/tree/8c535c615fea063b85257335128e97b73589b9fa)

### 범위

다음 저장소가 감사 범위에 포함되었습니다.

- https://github.com/celestiaorg/optimism/releases/tag/v1.3.0-OP_v1.9.2-CN_v0.15.0
- https://github.com/Uniswap/v3-core/tree/d8b1c635c275d2a9450bd6a78f3fa2484fef73eb
- https://github.com/Uniswap/v3-periphery/tree/0682387198a24c7cd63566a2c58398533860a5d1

# 발견 사항

# [L-01] 새 op-node 생성 시 daConfig에 대한 유효성 검사 누락

새 op-node를 생성할 때 `daConfig`를 제외한 모든 구성의 유효성이 검사됩니다.

```go
// Check verifies that the given configuration makes sense
func (cfg *Config) Check() error {
	...
	if err := cfg.AltDA.Check(); err != nil {
		return fmt.Errorf("altDA config error: %w", err)
	}
	if cfg.AltDA.Enabled {
		log.Warn("Alt-DA Mode is a Beta feature of the MIT licensed OP Stack.  While it has received initial review from core contributors, it is still undergoing testing, and may have bugs or other issues.")
	}
	if err := cfg.DaConfig.Check(); err != nil { // @audit no validate config
		return fmt.Errorf("da config error: %w", err)
	}
	return nil
}
```

daConfig에 대한 `Check()` 함수는 비어 있습니다.

```go
func (c CLIConfig) Check() error {
	return nil
}
```

`daConfig` 구조체의 `RpcURL` 또는 `FallbackMode`와 같은 구성의 유효성을 검사하는 것이 좋습니다.

# [L-02] op-batcher driver.sendTransaction에서 AltDA가 활성화된 경우의 중복성

`op-batcher/batcher/driver.go`에서 AltDA가 활성화된 경우 sendTransaction의 현재 흐름은 다음과 같습니다. 원래 트랜잭션 데이터가 AltDA 서버로 전송되고, 서버는 약정(commitment)을 반환합니다. 그런 다음 이 약정이 Celestia로 전송됩니다.

```go
		if l.Config.UseAltDA {
			comm, err := l.AltDA.SetInput(ctx, data)
			if err != nil {
				l.Log.Error("Failed to post input to Alt DA", "error", err)
				// requeue frame if we fail to post to the DA Provider so it can be retried
				l.recordFailedTx(txdata.ID(), err)
				return nil
			}
			l.Log.Info("Set AltDA input", "commitment", comm, "tx", txdata.ID())
			// signal AltDA commitment tx with TxDataVersion1
			data = comm.TxData()
		}
		candidate, err = l.celestiaTxCandidate(data)
		if err != nil {
			l.Log.Error("celestia: blob submission failed", "err", err)
			candidate, err = l.fallbackTxCandidate(ctx, txdata)
			if err != nil {
				l.Log.Error("celestia: fallback failed", "err", err)
				l.recordFailedTx(txdata.ID(), err)
				return nil
			}
		}

```

두 가지 시나리오가 있습니다.

`이중 약정 저장 사례`: AltDA가 사용되고 Celestia에 AltDA 약정 제출이 성공하면 약정(트랜잭션 데이터가 아님)이 Celestia에 저장됩니다. 약정이 AltDA와 Celestia 모두에 저장되므로 중복이 발생합니다. 따라서 롤업 노드가 L1 데이터에서 L2 블록을 재구성하려고 할 때 첫 번째 바이트가 `0xce`이므로 Celestia(AltDA가 아님)에서 데이터를 검색합니다. 그러나 이 데이터는 실제 트랜잭션 데이터가 아닌 AltDA 약정입니다.
[링크](https://github.com/celestiaorg/optimism/blob/04a1755dcfff1397bfc7b55a0960434cd170a5bc/op-node/rollup/derive/calldata_source.go#L106-L139)

`AltDA 우회 사례`: AltDA 약정을 저장하려고 시도하는 동안 Celestia 제출이 실패하면 AltDA가 올바르게 작동하더라도 AltDA 약정 대신 L2 트랜잭션 데이터가 L1으로 전송됩니다. 이는 AltDA가 완전히 우회됨을 의미합니다.

AltDA가 활성화된 경우 중복 Celestia 저장 없이 AltDA에만 저장하는 것이 좋습니다.
