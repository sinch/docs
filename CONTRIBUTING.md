Documentation contribution guidelines
========================

Guidelines to help you make awesome documentation!

The purpose for these guidelines is to:

-   Improve integration with third-party services.

-   Make it easy to edit existing content and publish new content.

-   Enable others than engineers to edit and publish.

-   Plan in public:

    -   For feedback on what docs are most needed.

    -   To have the most helpful content on our first release.

    -   To make it easier to improve your documentation continuously.

We use GitHub as the repository for our public-facing documentation.

Two types of documentation formats
----------------------------------

The two formats used at Sinch are Markdown and OAS/Swagger files.

### Markdown

Markdown:

* Is the standard for documenting APIs and frameworks on the web today. 

    <Note>
    
    **Note:** Sinch uses Markdown for all documentation types except API reference guides.
    
    </Note>
    
* Is lightweight and supports inline HTML. 
* Is used by numerous tools and services that enable us to author and publish markdown files. We use the [CommonMark extensions of Markdown](https://commonmark.org/).


### OAS/Swagger

OAS 3 or 2 is the specification for our rest APIs.

OAS enables us to:

* Build API explorers for our documentation sites.

* Auto-generate Helper libraries.

* Design first in OAS, or use tools for your language to generate dynamic Swagger at build or runtime.

You can find a list of tools [here](#tools).

Best practices and tone of voice
--------------------------------

* Check spelling and grammar in your articles, even if you must copy and paste into Microsoft Word to check.

* Use a casual and friendly tone, as if you're talking with another person.

* Use simple sentences. Easy-to-read sentences mean the reader can quickly use the guidance you share.

### Use a conversational tone

#### Appropriate style

We want our documentation to have a conversational tone. You should feel as though you are having a conversation with the author when you read our content. For you, the experience should be informal, conversational, and informative.

#### Inappropriate style

One might see the contrast between a conversational style and the style one finds with more academic treatments of technical topics. Those resources are very useful, but the authors have written those articles in a very different style than our documentation. When one reads an academic journal, one finds a very different tone and a very different style of writing. One feels that they are reading a dry account of a very dry topic.

The first paragraph above follows our recommended conversational style. The second is a more academic style. You see the difference immediately.

### Write in second person

Write as you speak to the reader.

Here are examples of writing in second person; in do-it-yourself or how-to writing:

* To make lemonade, you add the juice of lemons to water and sugar.

* You need to prepare a wall before applying the primer.

* When getting rid of a drain clog, first turn off the water.

* To calculate the area of a room, multiply the width by the length.

As you see it doesn’t always have to be “you” in a sentence to make it second person.

* To add oil to your car engine, unscrew the cap, place a funnel inside, and slowly add the oil.

### Focus on intent

Developers have a specific task in mind when they come to our documentation. Before you start writing, take a moment to think about who the developer is and
what he or she is trying to do.

### Write short

Don’t add words that are not useful, such as great, a lot, or qualifiers. If you need to use a qualifier, put it in the beginning of the sentence.

### Use active voice

Active voice:

* Always adds impact to your writing. 
* Makes it look like you are observing the action; that things are happening. 

The reader is more engaged when using active voice.


Passive voice:

* Makes it appear that people or objects are just waiting for stuff to happen to them. 

These active writing tips will help you make your work easy to understand and
coherent.

* Put the subject first. To change passive a sentence to active, flip the sentence around. That makes it clear who is performing the action. This also reduces the number of words in the sentence.

* Avoid the passive verb "to be." Complicated sentences with these helping verbs slow down the reader. More streamlined sentences keep the flow going.

* Swap -ing for -ed. Gerunds and present participles (words ending in -ing) tend to be more passive that verbs ending in -ed.

* Go easy on the adverbs. While it might be more descriptive to say "quickly walked" or "freshly baked," too many adverbs and intensifiers are another surefire way to slow down your writing. If your verb isn't strong enough on its own, choose a different one.

[Read more about active voice](https://grammar.yourdictionary.com/grammar-rules-and-tips/active-writing-tips.html).

### Screenshots

When you can use a screenshot to save words, do it!

### New concepts

When introducing a new service or concept. Explain the concept **before** you explain why it's useful. You need to understand what something is before you can understand the benefits of it.

### Use everyday words but not less technical

We have many users that do not speak English as their first language. Because of this, avoid Telecom-specific words and fancy English words.

Example: Use the term phone number over MSIDN.

You can use terms that are specific to the technology of the target developer. Not everyone reading your articles has a formal computer science degree. Explain
terms like 'idempotent' if you use it. For example:

“The Close() method is idempotent, meaning that you can call it many times and
the effect is the same as if you called it once.”

### Target level for English should be 5th grade

[How To enable Flesch-Kincaid Grade Level test](https://support.office.com/en-us/article/test-your-document-s-readability-85b4969e-e80a-4777-8dd3-f7fc3c8b3fd2)
in word, or use Hemmingway.

### Empathy and honesty

Focus on what matters to the customer, be supportive, and don’t use disclaimers if possible. Be open about areas that will frustrate our developers.

### Inclusive language

Avoid exclusive words. That is words that group people and make a person feel excluded from that group.

Acronyms that are specific for our industry are also excluding non-telecom developers.

Avoiding acronyms such as MSIDN, Bind, and 10DLC may not be possible. If you do introduce a new acronym, make sure you explain it.

For example: "When sending an SMS to to MSIDN over 10DLC, make sure you are compliant with FCC."

Better (still not great): "You need to understand the rules that the Federal Communications Commission have set up(link to a short version, and long version)
when you send SMS to a phone using a mobile number."

Thank you
---------

Thank you for reading these guidelines. We hope they help you, make your job easier, and make your products easier to use. If you need help or have feedback, reach out to me at Christian\@sinch.com.

Tools
-----

### Visual Studio Code Plugins Markdown and authoring

* <https://commonmark.org/>

* [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint), a popular linter by David Anson.

* [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker), an offline spell checker by Street Side Software.
    
### IntelliJ platform (WebStorm, PhpStorm, PyCharm, etc.)

* Jetbrains provides a Markdown plugin for editing with preview.

* [Markdown Navigator](https://plugins.jetbrains.com/plugin/7896-markdown-navigator) Much more powerful plugin with improved syntax highlighting and YAML frontmatter support (recommended!)

### Markdown Office integration

* <http://www.writage.com/> - Plugin for markdown.

* <https://support.office.com/en-us/article/test-your-document-s-readability-85b4969e-e80a-4777-8dd3-f7fc3c8b3fd2> - How to enable readability score.

### Swagger/OAS tools

* [https://app.swaggerhub.com](https://app.swaggerhub.com/)

* <http://apiworkbench.com/>

* <https://marketplace.visualstudio.com/items?itemName=mermade.openapi-lint>

* [http://swagger.io](http://swagger.io/)

* <http://editor.swagger.io>

* [https://inspector.swagger.io](https://inspector.swagger.io/)

* [https://stoplight.io](https://stoplight.io/)

* PHP code annotations - <https://github.com/zircote/swagger-php>

* .net code annotations - <https://github.com/domaindrivendev/Swashbuckle.AspNetCore>

### General tools for writing

* [http://grammarly.com](http://grammarly.com/)

* Hemmingway App <http://www.hemingwayapp.com/> 
