# Firewall Demo Go

This repository contains an empty Go module which can be used for safely
verifying that your [package firewall] is blocking malicious modules.

This module contains no malicious code, but it is marked as malicious in
Veracode's [Threat Feed]. If your package firewall is configured correctly, you
will not be able to install this module. If your package firewall is configured
incorrectly and you do install the module, no malicious code is downloaded as a
result.

## Usage

### 1. Configure your environment

Ensure your `GOPROXY` environment variable is configured as described in the
[Golang Proxy documentation]. This variable should always be set, not just
while installing this test module, to ensure that you are always protected.
You can view the current value by using the `go env GOPROXY` command.

### 2. Attempt to download this module

Use the `go mod download` command to download the module into your Go module
cache.

    go mod download -x github.com/veracode/firewall-demo-go@latest

This should fail:

```
go: module github.com/veracode/firewall-demo-go: reading https://golang.phylum.io/github.com/veracode/firewall-demo-go/@v/list: 424 "github.com/veracode/firewall-demo-go" failed Phylum analysis
    server response: "github.com/veracode/firewall-demo-go" failed Phylum analysis
```

If it fails, that means you are protected from installation of modules that
violate your [package firewall policy configuration].

## Troubleshooting

If the installation does not fail, it could be caused by one of the following
problems:

### `GOPROXY` is not set correctly

If the output of the `go mod download` command includes
`https://proxy.golang.org/` then your `GOPROXY` variable is not set correctly.
Verify that `go env GOPROXY` matches the [Golang Proxy documentation].

### `GOPRIVATE` or `GONOPROXY` are set too broadly

If the output of `go mod download` contains Git commands, that means `GOPROXY`
is not being used.

The `GOPRIVATE` and `GONOPROXY` variables are used to directly resolve modules
without using `GOPROXY`. This is required when using modules that are not
accessible to the proxy, for example if you have modules that are hosted
behind a VPN. See [Go's documentation on module privacy][go-mod-privacy] for
more details.

Setting either of these variables to include `*` or `github.com` will disable
the use of the proxy, and the package firewall, for potentially dangerous
modules. Ensure that `go env GOPRIVATE` and `go env GOPROXY` only apply to
trusted module locations.

### The "Confirmed Malicious" policy is disabled

In the [package firewall policy configuration], ensure that the policy named
"Confirmed Malicious," or some other policy that blocks at least [issue tag]
CM0038, is enabled and the "only warn" box is not checked.

This policy is enabled by default and is part of the default policy set
applied when not using a specific firewall instance.

[Golang Proxy documentation]: https://docs.phylum.io/package_firewall/golang
[Threat Feed]: https://docs.phylum.io/knowledge_base/threat_feed
[go-mod-privacy]: https://go.dev/ref/mod#private-module-privacy
[issue tag]: https://docs.phylum.io/knowledge_base/issue_tags
[package firewall]: https://docs.phylum.io/package_firewall/about
[package firewall policy configuration]: https://docs.phylum.io/knowledge_base/policy_usage
