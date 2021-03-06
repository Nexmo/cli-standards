# Nexmo API Standards

This document is heavily influenced by the [Heroku CLI Style Guide](https://devcenter.heroku.com/articles/cli-style-guide) which is well worth your time to read.

Contents of this standards document

* [User Stories for Nexmo CLI](#user-stories)
* [Packaging, installation and setup](#packaging-installation-and-setup)
* [Command design](#command-design)
* [Interactive commands](#interactive-commands)
* [Output format](#output-format)
* [Error handling](#error-handling)
* [Delight factor](#delight-factor)

## User Stories

To serve our users, it is important to think of the users and their needs first. These user stories have been assembled from a combination of how the CLI is used in our documentation and tutorials, and from the usage stats of the existing CLI tool.

> As a developer, I am considering Nexmo. I want to find out about installing, using and updating the CLI tool so I can understand if it fits our needs.

> As a JS developer, I want to use the Nexmo CLI to create an application and buy a number so I can follow a Nexmo tutorial.

> As a .NET developer, I want to use the Nexmo CLI to create an application and buy a number so I can follow a Nexmo tutorial.

> I have signed up for Nexmo but we use multiple providers. I want to look up prices for various operations so I can prepare price forecasts and decide which provider to use for which aprt of my application.

> As a developer, I want to receive SMS messages in my application.

> As a developer, I want to buy a number, and have my application respond to incoming calls on this number.

> As a developer, I use ngrok for local development. I don't have a paid account, so I often need to reconfigure my webhook URL (sometimes only one of them).

> As a developer, I am creating an application that sends and receives SMS, using the Messages API for outgoing messages.

> As a developer, I need to inspect the configuration of my account and may also want to change one or more settings.

> As a developer, I haven't used Nexmo since I first looked at it a few weeks ago. I want to look at which applications I have and which numbers are used by which application so that I can decide if I can reallocate or cancel numbers.

> As a developer I want to look up some information about a user's number for security purposes.

> I have been asked to detail for our auditors all the numbers on our Nexmo account and which applications use them. I need to supply this as a spreadsheet.

> As a developer, we run a large number of applications, and each one may have multiple incoming numbers linked to it. I want to know which numbers are linked to application X.


## Packaging, Installation and Setup

CLI tools **must**:

 - be available on all platforms (Windows, Mac, Linux - any others would be a bonus)
 - be distributable via common OS package managers
 - have as few dependencies as possible
 - be open to users of all our supported technology stacks (Ruby, PHP, Python, Java, NodeJS, .NET, Go, Android, iOS)

In addition, it would be nice if the tool could:

 - upgrade itself
 - be extensible with plugins
 - be relatively small in size
 - not store user's actual credentials on disk


### Version Info

The tool will be able to state its current version when `--version` is supplied

### Ops

The tool will have automated testing and be packageble/deployable across all the target platforms.

## Documentation

_[must have]_ Documentation. Installation and per-command documentation.

_[nice to have]_ Autogenerated documentation

### Inline Help

We will need top-level help, accessible from the following commands:
 
 * `--help`
 * `help`
 * `-h`
 * [no input]

The help will cover the top level and each available command. It can include examples, advice and humour.

## Command Design

Consistency and predictability are the key to a command line tools that users become familiar and comfortable with. To this end, all commands will follow the same conventions to enable us to build and maintain a tool that continues to feel familiar.

```
awesomecli topic:command --kebab-case-param value 
```

The above shows a basic template to use as a starting point

* **`topic`** will usually be a plural noun. Examples could include `applications`, `numbers`, `insights`.
* **`command`** will usually be an active or imperative verb. Examples could include `create`, `list`, `configure`.
* If a command can only accept one input parameter, it may be supplied without flags, as an additional shorthand to the standard named-flag approach.

The tool will support autocomplete so verbose is better. 

## Interactive Commands

**All commands must be available in non-interactive mode** to enable the tool to be used in automation.

For users who are not familiar with the tool, the tool offer interactive prompts for the parameters that a command supports. So for example using `number:search` without any further commands causes the tool to prompt the user for a country code (probably with a link to codes list, this might not be a great example!) and then offer the optional parameters with the option to accept the defaults. The tool should only go to interactive mode if the command is incomplete and would result in an error.

## Output Format

All commands must offer a `--silent` parameter to suppress all output.

All commands must offer a `--verbose` parameter to show additional output.

### Action Commands

Action commands return any output that is needed, even if it is just "OK". They also return an appropriate exit status.

### Data Comands

Any command that returns data should do so in the following ways.

**Single value**

A command that returns a single value can simply output that value.

**Multiple values**

Even for a single row, this data should be returned in columnar format, with headings. Example:

```
msisdn       | country | type       | features  | voiceCallbackType | voiceCallbackValue                   | moHttpUrl                                        | voiceStatusCallbackUrl
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
442038795229 | GB      | landline   | VOICE     | app               | 3050a697-f298-4601-8476-7805eafd33fc | undefined                                        | undefined             
442038931433 | GB      | landline   | VOICE     | app               | 048f0fb1-39a8-40c4-1149-6a2260771fa1 | undefined                                        | undefined             
442038931435 | GB      | landline   | VOICE     | app               | 048f0fb1-39a8-40c4-9d11-6a2260771fa1 | undefined                                        | undefined             
447418340330 | GB      | mobile-lvn | VOICE,SMS | undefined         | undefined                            | undefined                                        | undefined             
447418343340 | GB      | mobile-lvn | VOICE,SMS | undefined         | undefined                            | https://example.ngrok.io/webhooks/inbound-sms | undefined             
447418343838 | GB      | mobile-lvn | VOICE,SMS | app               | a7fb2d02-92d6-43b4-ae58-6011eae58bbc | https://example.ngrok.io/incoming/sms         | undefined             
```

This standard approach lends itself well to use with common commands such as `cut` and `tail` that are in common use by developers.

**Other Formats**

For commands that return multiple values, adding a `--format=tsv` parameter will return the same output in Tab-Separated Format.

_[nice to have]_ Adding a `--format=json` parameter will return the same output in JSON format.

**Pagination**

Especially for "power users" the size of the data responses can be large. All commands that return multiple values support common pagination parameters:
* `--limit` The number of records to return. Default: 100
* `--offset` How many records to skip before returning data. Default: 0

## Error Handling

When an error occurs, either in the CLI or in the underlying API response, the tool needs to respond in a predictable manner, including:

* return an error exit status
* show the detail of any error from the API

**Debug Mode**

Adding `--debug` will enable a diagnostic mode that writes additional information such as API requests and responses to `stderr`.

## Development Process

Will follow the same development and review process as the client libraries.

Must meet the coding standards for the chosen tech stack.

## Delight Factor

Tools are best when they assist the user to achieve their goals. The features in this section are here to enable developers to speedy success.

### Autocomplete

The tool must have autocomplete for:

 * _[must have]_ topics, subtopics and commands
 * _[must have]_ argument flags
 * _[nice to have]_ known values such as application IDs
 * _[nice to have]_ suggested commands when similar/typos are supplied

### Beyond the CLI

The tool needs more than just the tool.

Deliverables for a delightful CLI are:

 - open source code repo with tests, clear license, code of conduct, setup and contributing instructions.
 - build pipeline for pull requests and preparing releases
 - per-command level documentation, beyond the help output
 - Tutorials or HowTo content for common tasks. Writing these first from the user stories would help with interface design.
