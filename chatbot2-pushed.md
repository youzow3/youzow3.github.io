# Chatbot 2.0 is Pushed

2026/09/18

Recently, I pushed version 2.0 of Chatbot to
[my GitHub repo](https://github.com/youzow3/chatbot).
In this new version, I managed to fix some problems that occured because of
my lack of understanding of GLib/GObject.

The change is very simple: model abstraction got **better**.

Old abstraction was feature centric one. For example, there were
`LanguageModel` and `ToolCallableLanguageModel` interfaces. Latter interface
must be implemented if the model support tool calling. Why such abstraction
was used? because I thought feature centric one was better way to abstract
language models. Actually, it was not best option because you cannot implement
model with specific feature if the library doesn't know it. For instance,
Chatbot v1 lacks interfaces for vision, audio (ASR or just recognition), and so
model with such features were not able to be implemented.

Now, the version 2 uses agent centric abstraction and `LanguageModel` is just
one of backend that maybe implemented and used. To make agent centric, `Agent`
interface is added and models and agents act by receieving/returning `Message`
from/to caller. The `Message` uses MIME type to distinguish
content type, and the library doesn't need to define interface for specific
features. Also, implementers just need to implement `Agent` and/or
`LanguageModel`, and throw runtime error if the message(s) cannot be processed
with the model. This reduced boiler-plate for both Chatbot and the libraries
using Chatbot to implement language model and agent features.

Because of message format change, tool calling system is also changed.
Currently it still uses `GVariant` to represent function signatures, the
call parameters, and return values. As I think how LLM should interact with
world, just using JSON seems better option than converting between GVariant
and JSON back and forth. Though fixing it will become breaking change and at
least, it is working, I will fix it on version 3.

This is all for now. I'm currently working on
[Akatsuki](https://github.com/youzow3/akatsuki) to implement updated
`LanguageModel` and minimal tools for agentic use. If you are interested in 
how agent and language model are implemented, please check it.
