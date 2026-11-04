# Obfuscation of source code for proprietary trading platforms  
This report describes the possible paths of obfuscating your indicator/robot/plugin for a proprietary trading platform you embed it into.
In this case, the trading platform is cTrader, and the programming language is C#.

## Overview
Let us say you are developing the greatest trading robot the world has ever seen. You take extra precautions so that your source code remains secure and safe.
However, you might want to deploy your indicator to a machine that could become compromised. You want to preserve some degree of security. Various degrees of security exist, one of them being obfuscation.
According to the seminal 1997 paper[[1]](#1) on obfuscation, an obfuscation tool is
> a tool which converts a program into an equivalent one that is more difficult to understand and reverse engineer

This excellent paper introduces metrics such as potency, resilience, and cost.

## String Literal Obfuscation
We focus here on a relatively simple problem -- how to obfuscate a string literal from your source code, so that sensitive data is not trivially readable. This problem falls into the data obfuscation, in contrast to e.g. control flow obfuscation. In C#, the compiler generates a CIL (Common Intermediate Language) representation of every source program. Without obfuscation, the IL section contains in plain text all string literals, including e.g. private key, passwords, communication endpoints, etc. The data can be trivially read with tools like ILSpy by simply inspecting the generated DLL file (if you use standard compilation toolchains).

Obfuscation tools for string literals follow a well established scheme -- they replace the string literal in the IL section with obfuscated versions of that string, and a call to a deobfuscating function, which also resides somewhere in the IL section.
The potency, resilience and cost of the obfuscation depends on the function used to obfuscate/deobfuscate the string literals.
It is interesting to note that in recent studies[[2]](#2).  on the quality of LLM when targeting deobfuscation, string literal obfuscation turned out to be among the most challenging techniques for LLMs.

## Three Possible Directions

From a practical point of view, there are three possible directions you can take to introduce string literal obfuscation to your robot -- but the same three directions apply for all types of obfuscation:
* You can rely on the entire compiler and obfuscation provided by the trading platform. This approach is completely udocumented, and the official terminology for this is "security through obscurity". The platform cTrader has zero documentation on the exact obfuscation techniques deployed, other than some general reassuring text. If you take this path, you basically:
  * Choose the embedded compiler provided by the platform. You opt into code obfuscation (using the GUI).
  * Get a proprietary .algo file (no .DLL is generated!), which you simply trust is secure enough.
* You do not want to pay, and integrate your own obfuscation, either by implementing it, or by relying on open-source tools. The landcape is rather messy and many tools are poorly documented. You need to invest a considerable amount of time, but you know exactly what happens. The steps are:
  * You use the standard .NET compilation process, which produces a normal DLL library.
  * You apply obfuscation on the generated DLL to produce an obfuscated DLL (which follows the principles of rewriting the IL described above).
* You pay for a commercial obfuscation product. You know what you get, and you probably get a superior degree of obfuscation, even for the fact that you have a clear spec what the obfuscator you purchased actually does.

## References
<a id="1">[1]</a> Collberg, Christian, Clark Thomborson, and Douglas Low. "A taxonomy of obfuscating transformations." 9 Jul. 1997
<a id="2">[2]</a> Tkachenko, Anton, Dmitrij Suskevic, and Benjamin Adolphi. "Deconstructing Obfuscation: A four-dimensional framework for evaluating Large Language Models assembly code deobfuscation capabilities." arXiv preprint arXiv:2505.19887 (2025).
