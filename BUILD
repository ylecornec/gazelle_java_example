package(default_visibility = ["//visibility:public"])

#java_binary(
#    name = "ProjectRunner",
#    srcs = glob(["src/main/java/com/example/*.java"]),
#)
load("@bazel_gazelle//:def.bzl", "DEFAULT_LANGUAGES", "gazelle", "gazelle_binary")

# gazelle:prefix com.mycompany.app
# gazelle:java_maven_install_file maven_install.json
gazelle(
    name = "gazelle",
    gazelle = ":gazelle_bin",
)

gazelle_binary(
    name = "gazelle_bin",
    languages = DEFAULT_LANGUAGES + [
        "@contrib_rules_jvm//java/gazelle",
    ],
)
