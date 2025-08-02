# PredictiFi Protocol

## Decentralized Bitcoin Price Prediction Markets with Automated Rewards

![Stacks](https://img.shields.io/badge/Stacks-Bitcoin-orange) ![Clarity](https://img.shields.io/badge/Clarity-Smart%20Contract-blue) ![License](https://img.shields.io/badge/License-MIT-green)

## Overview

PredictiFi revolutionizes crypto forecasting by creating liquid prediction markets where traders stake STX tokens on Bitcoin's future price movements. Our protocol leverages Stacks' security model while providing instant settlements, dynamic liquidity pools, and algorithmic reward distribution. Perfect for DeFi enthusiasts seeking to monetize their market insights through transparent, oracle-driven price discovery mechanisms.

## Features

### Core Functionality

- **🎯 Binary Price Predictions**: Stake STX on Bitcoin price movements (Bull/Bear)
- **💰 Automated Reward Distribution**: Proportional payouts to winning participants
- **🔒 Escrow Security**: Secure STX token custody during market periods
- **⚡ Oracle Integration**: Real-time Bitcoin price feeds for settlement
- **📊 Dynamic Pool Management**: Automatic liquidity balancing

### Advanced Features

- **🛡️ Protocol Fee System**: Configurable fee collection (default 2%)
- **🎛️ Administrative Controls**: Market parameters and oracle management
- **📈 Market Analytics**: Comprehensive position and pool tracking
- **🔐 Access Control**: Role-based administrative functions

## Architecture

### Smart Contract Structure

```
PredictiFi.clar
├── Constants & Error Management
├── Protocol State Variables
├── Core Data Structures
│   ├── prediction-markets (map)
│   └── participant-positions (map)
├── Primary Functions
│   ├── initialize-prediction-market
│   ├── submit-forecast
│   ├── settle-market-outcome
│   └── claim-forecast-rewards
├── Read-Only Functions
└── Administrative Functions
```

### Data Models

#### Prediction Market Schema

```clarity
{
  initial-btc-price: uint,      // Opening Bitcoin price snapshot
  final-btc-price: uint,        // Settlement price (post-resolution)
  bullish-pool: uint,           // Total STX staked on price increase
  bearish-pool: uint,           // Total STX staked on price decrease
  market-open-height: uint,     // Block height when market opens
  market-close-height: uint,    // Block height when market closes
  is-resolved: bool             // Market settlement status
}
```

#### Participant Position Schema

```clarity
{
  price-direction: string-ascii, // Forecast: "bull" or "bear"
  staked-amount: uint,          // STX tokens committed
  rewards-claimed: bool         // Payout status tracker
}
```

## Quick Start

### Prerequisites

- [Clarinet](https://github.com/hirosystems/clarinet) - Stacks smart contract development environment
- [Node.js](https://nodejs.org/) (v16 or higher)
- [Stacks Wallet](https://www.hiro.so/wallet) for mainnet interaction

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/chidomere-ndubuisi/PredictiFi.git
   cd PredictiFi
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Verify contract syntax**

   ```bash
   clarinet check
   ```

4. **Run tests**

   ```bash
   npm test
   ```

### Development Setup

1. **Start local development environment**

   ```bash
   clarinet console
   ```

2. **Deploy to testnet**

   ```bash
   clarinet deploy --testnet
   ```

## Usage Guide

### For Market Participants

#### 1. Submit a Forecast

```clarity
(contract-call? .PredictiFi submit-forecast
  u1                    ;; market-id
  "bull"               ;; direction ("bull" or "bear")
  u5000000             ;; stake-amount (5 STX in uSTX)
)
```

#### 2. Claim Rewards

```clarity
(contract-call? .PredictiFi claim-forecast-rewards u1)
```

#### 3. Check Market Status

```clarity
(contract-call? .PredictiFi get-market-data u1)
```

#### 4. View Your Position

```clarity
(contract-call? .PredictiFi get-participant-position
  u1                    ;; market-id
  'ST1PQHQKV0RJXZFY1DGX8MNSNYVE3VGZJSRTPGZGM ;; participant
)
```

### For Market Creators (Admin Only)

#### 1. Initialize New Market

```clarity
(contract-call? .PredictiFi initialize-prediction-market
  u50000               ;; current BTC price in USD
  u1000                ;; market open height
  u2000                ;; market close height
)
```

#### 2. Settle Market

```clarity
(contract-call? .PredictiFi settle-market-outcome
  u1                   ;; market-id
  u52000               ;; final BTC price
)
```

## Configuration

### Protocol Parameters

| Parameter | Default Value | Description |
|-----------|---------------|-------------|
| `min-participation-threshold` | 1,000,000 uSTX (1 STX) | Minimum stake required |
| `protocol-fee-rate` | 2% | Fee collected from rewards |
| `price-oracle-endpoint` | Configurable | Authorized oracle address |

### Administrative Functions

- **Update Oracle**: `update-oracle-endpoint`
- **Adjust Minimum Stake**: `adjust-min-stake`
- **Modify Fee Rate**: `modify-fee-rate`
- **Withdraw Fees**: `withdraw-protocol-fees`

## Error Codes

| Code | Constant | Description |
|------|----------|-------------|
| u100 | `ERR-UNAUTHORIZED-ACCESS` | Access control violation |
| u101 | `ERR-RESOURCE-NOT-FOUND` | Missing market/prediction data |
| u102 | `ERR-INVALID-FORECAST` | Malformed prediction parameters |
| u103 | `ERR-MARKET-INACTIVE` | Market outside active window |
| u104 | `ERR-REWARD-CLAIMED` | Duplicate payout attempt |
| u105 | `ERR-INSUFFICIENT-FUNDS` | Inadequate STX balance |
| u106 | `ERR-INVALID-INPUT` | Parameter validation failure |

## Testing

### Run Test Suite

```bash
npm test
```

### Contract Validation

```bash
clarinet check
```

### Test Coverage

- Market initialization and validation
- Forecast submission and validation
- Oracle settlement mechanisms
- Reward calculation and distribution
- Administrative function access control
- Edge cases and error conditions

## Security Considerations

### Access Control

- **Administrative Functions**: Protected by `PROTOCOL_ADMIN` constant
- **Oracle Operations**: Restricted to authorized oracle endpoint
- **User Operations**: Validated against market state and timing

### Fund Security

- **Escrow Pattern**: User funds held in contract during market periods
- **Atomic Operations**: All transfers use `try!` for transaction safety
- **Balance Validation**: Comprehensive balance checks before transfers

### Market Integrity

- **Timing Validation**: Strict enforcement of market open/close windows
- **Double-Claiming Prevention**: Position-based claim tracking
- **Input Validation**: Comprehensive parameter validation

## Deployment

### Testnet Deployment

```bash
clarinet deploy --testnet
```

### Mainnet Deployment

```bash
clarinet deploy --mainnet
```

### Post-Deployment Setup

1. Configure oracle endpoint
2. Set initial protocol parameters
3. Initialize first prediction market
4. Verify administrative controls

## API Reference

### Public Functions

#### Core Market Functions

- `initialize-prediction-market(btc-price, open-height, close-height)` → `market-id`
- `submit-forecast(market-id, direction, stake-amount)` → `boolean`
- `settle-market-outcome(market-id, settlement-price)` → `boolean`
- `claim-forecast-rewards(market-id)` → `payout-amount`

#### Administrative Functions

- `update-oracle-endpoint(new-endpoint)` → `boolean`
- `adjust-min-stake(new-threshold)` → `boolean`
- `modify-fee-rate(new-rate)` → `boolean`
- `withdraw-protocol-fees(withdrawal-amount)` → `amount`

### Read-Only Functions

- `get-market-data(market-id)` → `market-object`
- `get-participant-position(market-id, participant)` → `position-object`
- `get-protocol-treasury()` → `balance`
- `get-protocol-settings()` → `settings-object`

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Workflow

1. Fork the repository
2. Create a feature branch
3. Write tests for new functionality
4. Ensure all tests pass
5. Submit a pull request

### Code Standards

- Follow Clarity best practices
- Include comprehensive comments
- Maintain test coverage above 90%
- Use semantic commit messages

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Roadmap

### Phase 1 (Current)

- ✅ Core prediction markets
- ✅ Binary price forecasting
- ✅ Oracle integration
- ✅ Automated settlements

### Phase 2

- 🔄 Multi-asset support
- 🔄 Advanced prediction types
- 🔄 Liquidity mining rewards
- 🔄 Governance token launch

### Phase 3

- 📋 Cross-chain integration
- 📋 Advanced analytics dashboard
- 📋 Mobile application
- 📋 Institutional features

---

**Built with ❤️ on Stacks | Securing Bitcoin's DeFi Future**
