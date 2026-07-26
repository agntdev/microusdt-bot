# KuCoin: USDT Rewards Bot — Bot specification

**Archetype:** custom

**Voice:** playful and concise — write every user-facing message, button label, error, and empty state in this voice.

A Telegram mini-app where users earn micro-USDT rewards through clicks, tasks, missions, and referrals. Features include ad-based income, commission-free withdrawals to TRC20 wallets, and anti-fraud verification thresholds. Admin receives withdrawal/fraud alerts via configured chat.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Telegram users seeking crypto micro-earnings
- TRC20 USDT holders
- Referral-driven task enthusiasts

## Success criteria

- Users complete 100+ daily missions
- 1000+ referral signups monthly
- 500+ successful withdrawals processed
- Ad network integration active

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Initialize account with optional referral code
- **Earn Missions** (button, actor: user, callback: missions:list) — Browse and complete reward missions
- **Watch Ads** (button, actor: user, callback: ads:list) — View rewarded advertisements
- **Withdraw USDT** (button, actor: user, callback: withdraw:form) — Initiate TRC20 wallet withdrawal
- **Invite Friends** (button, actor: user, callback: referrals:share) — Generate and share referral link

## Flows

### Onboarding
_Trigger:_ /start

1. Detect referral code if present
2. Show balance and quick actions
3. Display verification status

_Data touched:_ User

### Mission Completion
_Trigger:_ missions:list

1. Show available missions with rewards
2. User selects mission
3. Verify completion via proof or in-bot action
4. Credit USDT to balance

_Data touched:_ User, Mission

### Ad Interaction
_Trigger:_ ads:list

1. Display ad with reward
2. User views/clicks ad
3. Confirm with 'Claim' button
4. Credit reward if under daily limit

_Data touched:_ Ad, User

### Withdrawal Processing
_Trigger:_ withdraw:form

1. Verify anti-fraud thresholds met
2. Collect TRC20 wallet address
3. Process withdrawal with status tracking

_Data touched:_ Withdrawal, User

### Referral Tracking
_Trigger:_ referrals:share

1. Generate unique invite link
2. Track referee signups
3. Credit reward after referee's first mission

_Data touched:_ Referral, User

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **User** _(retention: persistent)_ — User profile and earnings data
  - fields: telegram_id, usdt_balance, verification_status, referral_code, mission_history
- **Mission** _(retention: persistent)_ — Configurable reward tasks
  - fields: title, reward_amount, daily_limit, completion_criteria
- **Ad** _(retention: persistent)_ — Third-party advertisement with reward
  - fields: content_url, reward_amount, daily_cap
- **Withdrawal** _(retention: persistent)_ — User payout request
  - fields: amount, wallet_address, status
- **Referral** _(retention: persistent)_ — Invite relationship tracking
  - fields: referrer_id, referee_id, reward_status

## Integrations

- **Telegram** (required) — Bot API messaging
- **TRC20 Wallet System** (required) — USDT payout processing
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Admin chat notifications for withdrawals/fraud
- Ad dashboard for campaign management
- Anti-fraud threshold configuration

## Notifications

- Withdrawal confirmation to user
- Admin alert for every withdrawal
- Fraud flag notifications
- Ad campaign performance updates

## Permissions & privacy

- User verification status required for withdrawals
- Referral activity tracking with opt-out
- Ad interaction data retention

## Edge cases

- Failed TRC20 transaction handling
- Referral code reuse prevention
- Ad view fraud detection
- Withdrawal rate limiting

## Required tests

- End-to-end withdrawal flow with fake TRC20
- Ad reward claim abuse scenarios
- Referral loop prevention
- Anti-fraud threshold triggers

## Assumptions

- Default anti-fraud: 3 missions or 7 days active before withdrawal
- Daily login reward: 0.001 USDT
- Ad reward cap: 10/day
- Withdrawal min: 1 USDT, max: 100 USDT/day
