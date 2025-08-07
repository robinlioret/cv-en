This is an rough idea for a database backup and refresh system on AWS and Kubernetes.

# Context

- Multiple separated AWS Accounts: 
    - Production
    - Non-production
    - Transit storage
    - Long term storage

/// admonition | Why multiple accounts?
    type: question

A multiple accounts configuration enables better and more secure separation of the workload and data. Which is a "must have" regarding backup policy and production isolation from lower environments.
///

# Diagrams

/// tab | Main layer
```mermaid
flowchart LR
  subgraph acc_prod["Production"]
    db_prod[(Database)]
    s3_prod[S3 Bucket<br/><i>Short term retention</i>]

    db_prod -->|full & binlog| s3_prod
    s3_prod -.->|restore recent| db_prod
  end

  subgraph acc_long_term_storage["Long term storage"]
    s3_lts[S3 Bucket<br/><i>Multi-region</i>]
  end

  s3_prod -->|replicate to| s3_lts
  s3_lts -.->|restore old| db_prod
```
///

/// tab | Anonymization layer
```mermaid
flowchart LR
  subgraph acc_prod["Production"]
    db_prod[(Database)]
    s3_prod[S3 Bucket<br/><i>Short term retention</i>]

    subgraph anonymization["Anonymization System"]
      sqs_prod(<i>SQS</i>)
      argo_event_prod(<i>ArgoEvent</i>)
      argo_workflow_prod(Anonymizer<br/><i>ArgoWorklow</i>)

      sqs_prod -->|trigger| argo_event_prod
      argo_event_prod -->|launch| argo_workflow_prod
    end

    db_prod -->|full & binlog| s3_prod
    s3_prod -.->|restore recent| db_prod

    s3_prod -->|publish to| sqs_prod
  end

  subgraph acc_long_term_storage["Long term storage"]
    s3_lts[S3 Bucket<br/><i>Multi-region</i>]
  end
  
  subgraph acc_transit_storage["Transit storage"]
    s3_ts[S3 Bucket<br/><i>Temporary storage</i>]
  end

  s3_prod -->|replicate to| s3_lts
  s3_lts -.->|restore old| db_prod
  argo_workflow_prod -->|push anonymized dumps| s3_ts
```
///

/// tab | Refreshing layer
```mermaid
flowchart LR
  subgraph acc_prod["Production"]
    db_prod[(Database)]
    s3_prod[S3 Bucket<br/><i>Short term retention</i>]

    subgraph anonymization["Anonymization System"]
      sqs_prod(<i>SQS</i>)
      argo_event_prod(<i>ArgoEvent</i>)
      argo_workflow_prod(Anonymizer<br/><i>ArgoWorklow</i>)

      sqs_prod -->|trigger| argo_event_prod
      argo_event_prod -->|launch| argo_workflow_prod
    end

    db_prod -->|full & binlog| s3_prod
    s3_prod -.->|restore recent| db_prod

    s3_prod -->|publish to| sqs_prod
  end

  subgraph acc_long_term_storage["Long term storage"]
    s3_lts[S3 Bucket<br/><i>Multi-region</i>]
  end
  
  subgraph acc_transit_storage["Transit storage"]
    s3_ts[S3 Bucket<br/><i>Temporary storage</i>]
  end

  subgraph acc_non_prod["Non-Production"]
    db_non_prod[(Database)]
  end

  s3_prod -->|replicate to| s3_lts
  s3_lts -.->|restore old| db_prod
  argo_workflow_prod -->|push anonymized dumps| s3_ts
  db_non_prod -->|pull| s3_ts
```
///