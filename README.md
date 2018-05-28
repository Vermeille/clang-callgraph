# clang-callgraph
A tool based on clang which generates a call graph from a given C++ codebase.

# Usage

`./clang-callgraph.py file.cpp|compile_commands.json [options] [extra clang args...]`

Understood `options` are:
* `-x name1,name2`: a comma separated list of excluded prefixes, like
  `std::,boost::`. All symbols starting with one of those will be hidden in the
  callgraph.
* `-p path1,path2`: a comma separated list of excluded prefixes, like
  `/usr`. All symbols defined or used in files whose name starts with of those
  will be hidden in the callgraph.

The easiest way to generate the file compile\_commands.json for any make based
compilation chain is to use [Bear](https://github.com/rizsotto/Bear) and recompile
with `bear make`.

When running the python script, after parsing all the codebase, you are
prompted to type in the function's name for which you wan to obtain the
callgraph

# Example

```
$ bear make
<output omitted>
$ clang-callgraph.py compile_commands.json -p /usr/lib/llvm-3.8/lib/clang/3.8.0/include/
reading source files...
/home/vermeille/CPAsim/src/module.cpp
/home/vermeille/CPAsim/src/module/modulevalues.cpp
/home/vermeille/CPAsim/src/main.cpp
/home/vermeille/CPAsim/src/parser.cpp
> main
matching:
main(int, char **)
> main(int, char **)
main(int, char **)
  Parser::ParseModuleDef(std::istream &)
    Parser::EatWord(std::istream &, const std::string &)
      Parser::FuckSpaces(std::istream &)
      Parser::EatChar(std::istream &, char)
    Parser::ParseWord(std::istream &)
      Parser::FuckSpaces(std::istream &)
    Module::Module(const std::string &)
    Parser::FuckSpaces(std::istream &)
    Parser::EatChar(std::istream &, char)
    Parser::FuckSpaces(std::istream &)
    Parser::EatChar(std::istream &, char)
    Module::AddInput(std::unique_ptr<WireDecl>)
      WireDecl::name()
      WireDecl::name()
      WireDecl::name()
    Parser::ParseWireDecl(std::istream &)
      Parser::ParseWord(std::istream &)
      Parser::FuckSpaces(std::istream &)
      Parser::EatChar(std::istream &, char)
      Parser::FuckSpaces(std::istream &)
      Parser::ParseDecimalInt(std::istream &)
        Parser::FuckSpaces(std::istream &)
      Parser::FuckSpaces(std::istream &)
      Parser::EatChar(std::istream &, char)
      Parser::FuckSpaces(std::istream &)
      WireDecl::WireDecl(const std::string &, int)
$ clang-callgraph.py compile_commands.json -x Parser:: -p /usr/lib/llvm-3.8/lib/clang/3.8.0/include/
reading source files...
/home/vermeille/CPAsim/src/module.cpp
/home/vermeille/CPAsim/src/module/modulevalues.cpp
/home/vermeille/CPAsim/src/main.cpp
/home/vermeille/CPAsim/src/parser.cpp
> main(int, char **)
main(int, char **)
  Module::BindUsagesToDef()
    Module::BindUsagesToDef_Rec(Expr *)
      Module::BindUsagesToDef_Rec(Expr *)
        Module::BindUsagesToDef_Rec(Expr *)
        Binop::lhs()
        Module::BindUsagesToDef_Rec(Expr *)
        Binop::rhs()
        Module::BindUsagesToDef_Rec(Expr *)
        Not::rhs()
        WireUsage::name()
        WireUsage::name()
        WireUsage::SetDeclRef(WireDecl *)
        WireUsage::IsUseValid()
          WireDecl::size()
          WireDecl::size()
          WireDecl::size()
        WireUsage::name()
        WireUsage::index()
        WireUsage::index()
        WireDecl::size()
```

