# Speed-up Jina App development in PyCharm and VSCode

## Set up your developer environment for jina

This chapter describes how to speed up your developer experience
in Pycharm and VS Code IDE while using Jina.
In particular we would focus on how to enable Intellisense for Jina YAML configs. Intellisense can provide code completion, showing number and arguments list, validating your YAML config.

These features can help you build your Jina application much faster.
Once you have the latest Jina version installed with PyPI, while creating a typical Jina application - two types of files need to be written: Python code and YAML config. The python
file defines the entry point and customized logics while the YAML configs define the flow composition and the configuration of each individual executor.
Depending on the cases and the depth of customization, you can develop the project in both ways. In Jina, it is even possible to build a completely code free project that only depends on the configs. Most IDEs have provided smart intelligence for python development - due to
straightforward API type hints and 100% string coverage in jina framework, you can enjoy a pretty smooth development experience. However, for YAML configs, it doesn't help so much besides simple code highlight which could slow down your development speed as you have to navigate through the documentation of the code base in order to figure out which component
or argument you really want to use.

### Enable Intellisense for jina yaml configs in Pycharm:

- First open Pycharm, click on the Menu and select Preference. 
- In the left panel, search for JSON schema in the search box and select JSON schema mappings.
- Next, in the right panel click + icon and add a new Schema.
- Name: Name as per choice (for eg. jina) 
- In the Schema file or URL write `api.jina.ai/schemas/latest.json` ( this ensures that your schema is always up to date with the new release).
- You can also bind it to the specific Jina version or pre-release version.
- In the Schema Version, select JSON Schema version 7.
- Finally, add to File path pattern mapping: *.jaml to associate any files that end with JAML or Jina yaml extensions to the schema.
- Click OK to apply the change.

![ide-speed-up](intelli.png "Enabling Intellisense")

### For Visual Studio Code developers:

- First install the YAML extension from the RedHat.
- Click on the sidebar and select Extension.
- Search for YAML and then select *YAML support* from RedHat and then install it.
- Open your IDE-wise `settings.json`, create a new entry:
```
“yaml.schemas”: {
“https://api.jina.ai/schemas/latest.json”: [“/*.jina.yml, /*.jaml]
}
```
- Finally save your `settings.json` to apply the change.

![ide-speed-up](vse.png "VSCode Autocomplete")


Create a new `hello.jina.yml` file and the IDE successfully marks it as Jina file type
If it doesn't, you may want to manually select the schema you just created.
Now type Jtype in the first line and you will see the IDE suggests you with flow or a list of executors that jina contains.

![ide-speed-up](pyc.png "Pycharm Autocomplete")

*jtype* is a synonym for the bond mark. We recommend you to use j-type over the bond mark as
it gives a cross-platform compatible yaml files.
Now, the IDE immediately marks it as yellow as it is not a valid jina config file.
Hover your mouse on it and it complains you haven't defined the required field version and parts.
It let's it complete them as you write urlc's autocomplete kicks in to help you fill in the default
values. You can hover your mouse on the field to see the help text, which is consistent with the
documentation.
You will also see the hint is based on the context: for example, when writing parts, it
suggests all arguments that are accepted by the part.
When you write something that is unrecognizable by the schema, the ID will mark it as yellow
immediately. The intellisense becomes super helpful when you write an executor level yaml
file.
Let's create a new YAML file for configuring Numpy indexer.
You can see the IDE will only keep the arguments that are accepted by that executor.
It also works on the nested level when you define request and it shows only drivers
and when you define the drivers it shows relevant arguments that are accepted by
that driver.

![ide-speed-up](pychvse.png "PyCharm and VS Code developer environments")

You can check out the log stream for any driver or executor by holding your mouse on it besides
Pycharm or VS Code. Most mainstream IDEs also support JSON schema. You can configure it manually, the actual user experience may slightly vary depending on your IDE or plugin
in general. 

A schema file enables code completion, syntax validation and argument filtering, filling default values and displaying help text.


