# Superior Byte Works Google TimesFM Forecasting

Superior Byte Works Google TimesFM Forecasting is the foundation-model forecasting skill pack for this repo. It wraps Google's TimesFM time-series model in a safer, agent-friendly workflow that checks hardware first, supports zero-shot forecasting, and produces both point forecasts and prediction intervals.

Use this skill when you need forecasting on univariate time series and you want a strong foundation-model baseline without training a custom model first.

## What This Skill Does

- Runs zero-shot forecasting on many kinds of time series without training a bespoke model.
- Forces a preflight system check before model loading so the workflow does not crash weak machines.
- Supports point forecasts plus quantile-based uncertainty bands.
- Covers common patterns like CSV forecasting, covariate-aware forecasting, anomaly screening via intervals, and evaluation.
- Gives the repo a modern forecasting tool that complements farm strategy and breeding workflows.

## Forecasting Workflow

```mermaid
flowchart TD
    accTitle: TimesFM Forecasting Workflow
    accDescr: Forecasting workflow that begins with a mandatory system check, then loads and configures TimesFM, prepares the time series, and returns forecasts with intervals.

    start([📈 Time series request]) --> check[🧪 Run system preflight]
    check --> ready{Resources available?}
    ready -->|Yes| load[Load TimesFM]
    ready -->|No| stop([⛔ Stop or downgrade plan])
    load --> configure[⚙️ Compile forecast config]
    configure --> input[Prepare time-series input]
    input --> forecast[Run forecast]
    forecast --> output([✅ Point forecast and quantile intervals])

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef process fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f
    classDef outcome fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    classDef block fill:#fee2e2,stroke:#dc2626,stroke-width:2px,color:#7f1d1d

    class start,check,ready primary
    class load,configure,input,forecast process
    class output outcome
    class stop block
```

The mandatory preflight check is part of what makes this skill useful in a real agent system. It avoids loading a large model blindly on machines that cannot handle it.

## Core Capability Areas

| Area                  | What it covers                                 | Typical output                      |
| --------------------- | ---------------------------------------------- | ----------------------------------- |
| Preflight             | RAM, GPU, disk, and install readiness          | go/no-go decision before model load |
| Zero-shot Forecasting | univariate forecasting without custom training | point forecast arrays               |
| Prediction Intervals  | quantile-based uncertainty ranges              | lower and upper bands               |
| Covariate Forecasting | exogenous-driver workflows for TimesFM 2.5+    | adjusted forecasts                  |
| Anomaly Screening     | unusual-value detection from quantile bands    | warning and critical flags          |
| Evaluation            | holdout and accuracy checks                    | MAE, RMSE, coverage summaries       |

## How It Fits In This Repo

```mermaid
flowchart LR
    accTitle: TimesFM Role In The Repo
    accDescr: Diagram showing how farm, weather, breeding, and operational time series flow into the TimesFM skill and produce horizon estimates and uncertainty bands for planning.

    farm_data([🌾 Farm and operational series]) --> timesfm[📈 TimesFM forecasting skill]
    timesfm --> horizon[Future horizon estimates]
    timesfm --> bands[Prediction intervals]
    horizon --> strategy[Planning and strategy decisions]
    bands --> risk[⚠️ Risk-aware interpretation]

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef support fill:#ecfccb,stroke:#65a30d,stroke-width:2px,color:#365314
    classDef outcome fill:#f3e8ff,stroke:#9333ea,stroke-width:2px,color:#581c87

    class farm_data,timesfm primary
    class horizon,bands support
    class strategy,risk outcome
```

This skill complements the rest of the repo by handling the forecasting layer. It is not the whole farm advisory system by itself; it is the forward-looking time-series engine you use when you need probabilistic outlooks.

## Typical Usage Patterns

```mermaid
flowchart LR
    accTitle: TimesFM Usage Patterns
    accDescr: Overview of the main ways the TimesFM skill is used, including basic forecasts, covariate forecasts, evaluation, and anomaly screening.

    series([📊 Input series]) --> basic[Basic forecast]
    series --> covariates[Forecast with covariates]
    series --> evaluate[Backtest and evaluate]
    series --> anomaly[Use quantile bands for anomaly checks]

    classDef primary fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a5f
    classDef support fill:#fef3c7,stroke:#d97706,stroke-width:2px,color:#78350f

    class series primary
    class basic,covariates,evaluate,anomaly support
```

Typical examples:

- demand or supply outlooks
- weather or climate trajectories
- sensor or operational trend forecasting
- anomaly screening using forecast intervals
- fast baseline forecasts before building a classical model

## Key Strengths

- fast path to useful forecasting
- probabilistic outputs rather than a single blind point estimate
- safe machine-checking before inference
- good fit for agent workflows because it can reason about readiness, forecast, and uncertainty together

## Start Here

- Main entrypoint: [`SKILL.md`](SKILL.md)
- Preflight requirement and quick start live in `SKILL.md`
- Examples live under `examples/`, including `examples/global-temperature/`
- Use this before moving to more specialized classical time-series tooling when zero-shot forecasting is enough
