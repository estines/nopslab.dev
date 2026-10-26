Product Requirements Document (PRD)

Project Name: Headless Algorithmic Trading Engine for Prop Firms

Target Environment: Local Execution / Dockerized

Primary Target Firm: FTMO

1. Project Overview

A highly performant, headless algorithmic trading and backtesting engine built in Go. The system is designed to secure and trade funded prop firm accounts without risking personal capital. It evaluates historical and live market data against predefined strategies, strictly enforces prop firm risk rules (e.g., FTMO), and outputs deterministic structured data rather than visual charts.

2. Technical Stack

Core Language: Go (Golang)

Data Source (Free): Yahoo Finance API (e.g., via github.com/piquette/finance-go or custom REST client)

Architecture Pattern: Domain-Driven Design (Data, Engine, Risk, Strategy modules)

Execution Target: Console/Stdout for backtesting; easily adaptable to MT5 via gRPC/ZeroMQ in the future.

3. Scope

In Scope:

Historical backtesting event loop.

Data ingestion and normalization for OHLCV bars.

Strict FTMO risk management tracking.

Extensible strategy interface.

Condition-match logging and trade event simulation.

Initial asset support for Gold (XAUUSD=X).

Out of Scope:

Graphical User Interfaces (GUIs) or price charts.

Live broker execution routing (Phase 2).

High-Frequency Trading (HFT) tick-level order book depth.

4. Functional Requirements

4.1 Data Management

REQ-D1: The system shall fetch historical OHLCV (Open, High, Low, Close, Volume) data from Yahoo Finance.

REQ-D2: The system must support configurable timeframes/resolutions (e.g., 15m, 1h, 1d) at the provider request level.

REQ-D3: The asset structure must be agnostic, allowing seamless addition of new assets beyond the initial Gold (XAUUSD) requirement (e.g., Forex pairs, Indices).

4.2 Strategy Engine

REQ-S1: Strategies must implement a standard Strategy interface containing an Evaluate([]Bar) method.

REQ-S2: When trade criteria are met, the strategy must output a ConditionMatch struct containing: Action (BUY/SELL), Entry Price, Stop Loss, Take Profit, Timestamp, Asset, Trigger Reason, and Indicator State.

REQ-S3: The engine must support running multiple strategies concurrently over the same dataset.

4.3 Risk Management (FTMO Compliance)

REQ-R1: The system must independently track a simulated account balance and equity.

REQ-R2 (Daily Limit): The system shall reject any trade signal that would risk exceeding a maximum daily loss of 5% based on the simulated start-of-day equity.

REQ-R3 (Total Limit): The system shall reject any trade signal that would risk exceeding a maximum total loss of 10% of the initial account balance.

REQ-R4 (Time Filters): The system must support logic to flatten (close all) positions before the weekend (Friday afternoon) to comply with non-swing account rules.

4.4 Backtesting Event Loop

REQ-B1: The engine shall iterate through historical data sequentially to simulate live market conditions (preventing look-ahead bias).

REQ-B2: The engine must manage a simulated portfolio, evaluating active trades against incoming bars to trigger Stop Loss (SL) or Take Profit (TP) events.

REQ-B3: The system shall output trade logs in a structured, readable format to the console for analysis.

5. Non-Functional Requirements

Extensibility: Adding a new asset or strategy should not require modifying the core event loop or risk manager.

Performance: The engine must process years of 15-minute historical data in seconds, leveraging Go's memory efficiency.

Security: No hardcoded API keys or broker credentials. Any future integrations must use environment variables (.env).