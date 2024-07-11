# Devscast Hexa Skeleton

This is a skeleton symfony project with Onion (hexagonal) Architecture, instead of the traditional layered architecture.
we use the following layers:

- application : contains the application services, which are the use cases of the application, commands, and queries and their handlers.
- domain : contains the domain entities, value objects, repositories (interfaces), and domain services.
- infrastructure : contains implementations of the domain repositories, and other infrastructure services like mailers, notifiers, etc.

each layer is independent of the other layers, and the dependencies are injected from the outer layer to the inner layer.
each layer contains a bounded context, which is a group of related use cases and entities.

Note : this architecture is inspired by the book "Domain-Driven Design: Tackling Complexity in the Heart of Software" by Eric Evans.
it's a simplified version of the onion architecture, which is a more general concept and thus no meant to be a strict implementation, 
we adapt it to our needs and the needs of ours projects.

**why Hexa instead of Onion? because it's shorter and sounds cool 😎**

## Usage

```bash
composer create-project devscast/symfony-hexa-skeleton my_project
```

## installed packages


### Support template generation with symfony/ux-twig-component

As for now, `symfony/ux-twig-component` does not support `{{ "<twig:component />" }}` syntax. 
which is used when generating templates with `bin/console hexa:make:template [index|show]`.
To make it work, we need to patch the `symfony/ux-twig-component` package.

```json
{
  "extra": {
    "patches": {
      "symfony/ux-twig-component": {
        "printed raw component": "./patches/ux-twig-component.patch"
      }
    },
    "composer-exit-on-patch-failure": true
  }
}
```

create patch file `./patches/ux-twig-component.patch` with the following content

```diff
--- a/vendor/symfony/ux-twig-component/src/Twig/TwigPreLexer.php	2024-02-29 18:20:59.000000000 +0200
+++ b/vendor/symfony/ux-twig-component/src/Twig/TwigPreLexer.php	2024-05-09 13:37:06.643585125 +0200
@@ -70,6 +70,17 @@
                 }
             }

+            if ($this->consume('{{ "')) {
+                $output .= '{{ "';
+                $output .= $this->consumeUntil('" }}');
+                $this->consume('" }}');
+                $output .= '" }}';
+
+                if ($this->position === $this->length) {
+                    break;
+                }
+            }
+
             if ($this->consume('{% embed')) {
                 $inTwigEmbed = true;
                 $output .= '{% embed';
```
