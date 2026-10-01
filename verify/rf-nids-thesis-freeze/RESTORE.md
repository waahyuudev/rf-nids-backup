# RF-NIDS Thesis Final Freeze

## Frozen Baseline

- Branch: development
- Commit: d14a821c76865f289a7d8b4c82271125f15a0c90
- Database: PostgreSQL `rf_nids`
- Final thesis runtime model: RF-v5 (`rf-v5-candidate-01`)
- RF-v5 SHA256:
  `31d7d50fa79e3400e7d357cab05c780e038bc527b746d0be6ff894828c6b8d17`

## Important Scientific Status

RF-v5 remains a scientific candidate and was not promoted to the default active model.

Scientific decision:

`RF_V5_VALIDATION_FAIL`

RF-v5 was manually selected for the frozen runtime demonstration.

## Frozen Runtime Sessions

- Session 20: Normal HTTP
  - 58 flows
  - 46 Normal
  - 12 PortScan
  - 12 MEDIUM alerts

- Session 21: controlled HTTP DDoS-like/load profile
  - 535 flows
  - 453 DDoS
  - 82 Normal
  - 453 HIGH alerts

- Session 22: bounded PortScan
  - 1000 flows
  - 1000 PortScan
  - 1000 MEDIUM alerts

Session 22 provenance note:
persisted monitoring target was 10.10.10.2 while the bounded Nmap command targeted 10.10.20.2.

## Restore Source Code

Clone the repository:

    git clone https://github.com/waahyuudev/rf-nids.git
    cd rf-nids

Restore the frozen baseline:

    git checkout d14a821c76865f289a7d8b4c82271125f15a0c90

Verify:

    git rev-parse HEAD
    git status

## Restore Configuration

Use:

    config/.env.example
    manifests/runtime-config.txt
    manifests/environment.txt

Create the runtime `.env` for the restored host.

Host-specific values such as `DOCKER_SOCKET_GID` and
`RUNTIME_MONITORING_HOST_ROOT` may need to be adjusted for the restored machine.

Do not assume that host-specific paths or Docker group IDs remain identical.

## Restore RF-v5

Frozen model:

    models/experiment_f/random_forest_rf_v5_candidate_01.joblib

Expected SHA256:

    31d7d50fa79e3400e7d357cab05c780e038bc527b746d0be6ff894828c6b8d17

Verify with:

    sha256sum models/experiment_f/random_forest_rf_v5_candidate_01.joblib

## Restore Database

Database dump:

    database/rf_nids_final.dump

Database name:

    rf_nids

After PostgreSQL is available, restore using `pg_restore`.

Example:

    cat database/rf_nids_final.dump | \
    docker compose exec -T postgres \
    pg_restore -U postgres -d rf_nids --clean --if-exists

Perform database restore only on a recovery environment.
Do not run this command against the preserved original database.

## Evidence

Scientific RF-v5 evidence is stored under:

    evidence/experiment_f/rf_v5_candidate_01/
    evidence/experiment_f/normal_remediation_a2/

Runtime demonstration evidence is stored under:

    evidence/experiment_f/runtime_demo/rf_v5_sessions_20_22/

These evidence files must be preserved unchanged.

## CICFlowMeter Provenance Note

The frozen runtime evidence records the extractor provenance used by the
runtime sessions.

The current `.env` runtime configuration may contain a different
CICFlowMeter image digest. Preserve both records as-is. Do not rewrite
historical runtime evidence to match the current environment.

## Verification

After recovery verify:

1. Git commit matches the frozen commit.
2. RF-v5 SHA256 matches the frozen model hash.
3. PostgreSQL database restores successfully.
4. Runtime evidence Session 20, 21 and 22 remains available.
5. Docker services start successfully.
6. API health check succeeds.
7. Scientific evidence and runtime evidence remain unchanged.

