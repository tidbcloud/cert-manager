# gazelle:repository_macro hack/build/repos.bzl%go_repositories
workspace(name = "com_github_jetstack_cert_manager")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")

http_archive(
    name = "com_google_protobuf",
    sha256 = "2ee9dcec820352671eb83e081295ba43f7a4157181dad549024d7070d079cf65",
    strip_prefix = "protobuf-3.9.0",
    urls = ["https://github.com/protocolbuffers/protobuf/archive/v3.9.0.tar.gz"],
)

load("@com_google_protobuf//:protobuf_deps.bzl", "protobuf_deps")

protobuf_deps()

## Load rules_go and dependencies
http_archive(
    name = "io_bazel_rules_go",
    urls = [
        "https://storage.googleapis.com/bazel-mirror/github.com/bazelbuild/rules_go/releases/download/v0.19.5/rules_go-v0.19.5.tar.gz",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.19.5/rules_go-v0.19.5.tar.gz",
    ],
    sha256 = "513c12397db1bc9aa46dd62f02dd94b49a9b5d17444d49b5a04c5a89f3053c1c",
)

load("@io_bazel_rules_go//go:deps.bzl", "go_rules_dependencies", "go_register_toolchains")

go_rules_dependencies()

go_register_toolchains(
    go_version = "1.13.1",
    nogo = "@//hack/build:nogo_vet",
)

## Load gazelle and dependencies
http_archive(
    name = "bazel_gazelle",
    url = "https://github.com/bazelbuild/bazel-gazelle/releases/download/0.18.2/bazel-gazelle-0.18.2.tar.gz",
    sha256 = "7fc87f4170011201b1690326e8c16c5d802836e3a0d617d8f75c3af2b23180c4",
)

load("@bazel_gazelle//:deps.bzl", "gazelle_dependencies")

gazelle_dependencies()

## Load kubernetes repo-infra for tools like kazel
git_repository(
    name = "io_k8s_repo_infra",
    commit = "967e39a37fb93640a37e272949ddf92a8c96f230",
    remote = "https://github.com/kubernetes/repo-infra.git",
    shallow_since = "1569300445 -0700",
)
## Load rules_docker and dependencies, for working with docker images
http_archive(
    name = "io_bazel_rules_docker",
    sha256 = "4521794f0fba2e20f3bf15846ab5e01d5332e587e9ce81629c7f96c793bb7036",
    strip_prefix = "rules_docker-0.14.4",
    urls = ["https://github.com/bazelbuild/rules_docker/releases/download/v0.14.4/rules_docker-v0.14.4.tar.gz"],
)

load(
    "@io_bazel_rules_docker//repositories:repositories.bzl",
    container_repositories = "repositories",
)

container_repositories()

load("@io_bazel_rules_docker//repositories:deps.bzl", container_deps = "deps")

container_deps()

load("@io_bazel_rules_docker//repositories:pip_repositories.bzl", "pip_deps")

pip_deps()

load(
    "@io_bazel_rules_docker//container:container.bzl",
    "container_pull",
)
load("@io_bazel_rules_docker//go:image.bzl", _go_image_repos = "repositories")

_go_image_repos()

## Use 'static' distroless image for all builds
container_pull(
    name = "static_base",
    registry = "gcr.io",
    repository = "distroless/static",
    digest = "sha256:cd0679a54d2abaf3644829f5e290ad8a10688847475f570fddb9963318cf9390",
)

# Load and define targets defined in //hack/bin
load(
    "//hack/bin:deps.bzl",
    install_hack_bin = "install",
)

install_hack_bin()

# Load and define targets defined in //hack/bin
load(
    "//test/e2e:images.bzl",
    install_e2e_images = "install",
)

install_e2e_images()

# Load and define targets used for reference doc generation
load(
    "//docs/generated/reference:deps.bzl",
    install_docs_dependencies = "install",
)

install_docs_dependencies()

# The npm_install rule runs yarn anytime the package.json or package-lock.json file changes.
# It also extracts any Bazel rules distributed in an npm package.
load("@build_bazel_rules_nodejs//:defs.bzl", "npm_install")

npm_install(
    # Name this npm so that Bazel Label references look like @brodocs_modules//package
    name = "brodocs_modules",
    package_json = "@brodocs//:package.json",
    package_lock_json = "//docs/generated/reference/generate/bin:package-lock.json",
)

# Install any Bazel rules which were extracted earlier by the npm_install rule.
load("@brodocs_modules//:install_bazel_dependencies.bzl", "install_bazel_dependencies")

install_bazel_dependencies()

load("//hack/build:repos.bzl", "go_repositories")

go_repositories()
