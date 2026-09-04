# Security & Cost

## Secrets

Never commit:

- VPN PSKs
- AD passwords
- IAM access keys
- private keys
- `.pem` files
- registration credentials
- RADIUS shared secrets

The AWS VPN configuration file contains sensitive authentication material and should remain outside the public repository.

## IAM

The lab uses the `chandan-admin` IAM identity through the AWS CLI login flow.

Long-lived IAM access keys were intentionally avoided.

MFA for the administrative IAM identity remains a hardening task.

## Network security

Current firewall rules were deliberately broad enough to establish connectivity.

After functional validation, tighten them to the minimum required:

- source networks
- destination networks
- required AD services
- WorkSpaces-related traffic
- logging where useful

## VPN

The VPN is currently behind an upstream NAT environment because the Sophos WAN address is private.

NAT-T is therefore important.

Tunnel 2 is down and is documented as a known resilience gap.

## WorkSpaces cost control

The lab should:

1. Use AutoStop.
2. Start with one WorkSpace.
3. Avoid AlwaysOn unless intentionally testing it.
4. Verify the current AWS Free Tier eligibility for the selected bundle.
5. Monitor AWS billing.
6. Terminate temporary resources immediately after tests.
7. Review the account for orphaned test resources.

Do not treat an AWS Free Tier statement as universal for every bundle. The selected bundle, OS, running mode, storage and current AWS terms all matter.

## Cleanup checklist

- [ ] WorkSpace stopped/removed when no longer needed
- [ ] Temporary EC2 SG removed
- [ ] Temporary EC2 key pair reviewed/removed
- [ ] Local `.pem` removed if no longer needed
- [ ] Tunnel 2 decision documented
- [ ] Unused security groups removed
- [ ] Billing dashboard reviewed
