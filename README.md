# configleaf

## Software configuration for Clojure.

Sometimes you need to write software that behaves differently depending on the context
it is being used in. For example, when I write a web service (for example), I want the
servlet to open additional ports and output additional debugging information on errors
when I am working on developing it, but not when I push it into production.

Configleaf lets you easily define configurations, ordinary Clojure data structures, that
bundle together any number of options and flags that can drive the behavior of your
software, and lets you control it from Leiningen (hopefully Cake as well soon). Your
software will not require a dependency on configleaf (only a dev-dependency). It will 
instead have a new namespace containing the values for the currently active configuration.

## Usage

There are two steps to using Configleaf. First, you need to add some key/value pairs to
your project definition in project.clj. The `:configleaf` key should contain a map with
the following:

* `:configurations` A map of configuration names to values that contain all the data
needed in that configuration. This can include data (key: `:data`) you want at runtime
(arbitrary key-value pairs), or Java system properties (key: `:properties`) you'd like 
set during development (string-string pairs). For example, 

```clojure
    :configleaf {:configurations {:dev {:data {:listen-port 8080}
                                        :properties {"pallet.admin.username" "don"}}
                                  :prod {:data {:listen-port 80}}}}
```
* `:namespace` The namespace to insert the active configuration into. Optional. Defaults
to `configleaf.config`.
* `:default` The default configuration to use when none is specifically active. Optional.

As an example, consider the following.

    :configleaf {:default :dev
                 :namespace cfg.myproject
                 :configurations {:dev {:data {:db "127.0.0.1"}}
                                  :prod {:data {:db "db.myproject.com"}}}}
                                  
With this configuration, when you run the code (from a jar, repl, or swank environment),
you can access the active configuration by accessing the values `config` and `config-data`
in the namespace `cfg.myproject`. The value `config` is the name of the active 
configuration (in the example above, `:dev` or `:prod`), and `config-data` is the data
from the active configuration (such as `{:db "127.0.0.1"}`). 

The second part of using Configleaf is controlling the active configuration from the
command line. There are just a few commands.

* `lein configleaf status` Prints the currently active configuration. 

```
    lein configleaf status
    Configleaf
      Current configuration:  :test
```

* `lein configleaf set-config [config]` Sets the currently active configuration to config.

## Installation

To install Configleaf, add the following to your dev-dependencies

    [configleaf "0.2.0"]
    
After you set up the configleaf key in your project (see above), you will probably also
want to add two directories to your `.gitignore` file. The first is the directory
`.configleaf`, which will be in the same directory as your project.clj. This directory
holds the currently active configuration. The second is the namespace that is
automatically generated by Configleaf with your configuration values. In the example
above, you'd want to add "src/cfg/myproject.clj" or possibly "src/cfg" to your
`.gitignore`.

Finally, in your project.clj, add the following key-value pair:

    :hooks [configleaf.hooks]
    
This will ensure that Configleaf's functionality runs when you run leiningen.

## News

* Version 0.2.0
  * Addition of Java system properties to configurations.
  * Changes to configuration map format to allow system properties. 

## License

Copyright (C) 2011

Distributed under the Eclipse Public License, the same as Clojure.
