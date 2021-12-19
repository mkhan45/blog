---
title: "Expression Evaluator"
date: 2021-02-22
# weight: 1
# aliases: ["/first"]
tags: ["cool-code-snippets", "scala", "programming-languages"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/expression-evaluator"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---

I've been working with compilers and interpreters a lot recently, and I think people usually overstate the difficulty, so I wanted to write about the core starting point of every interpreter: a basic expression evaluator. For a compiler this would be a basic expression *compiler* instead, but they turn out to be pretty similar.

Generally functional languages are best suited to this problem because it lends itself to pattern matching and sum types, but it works out fine in OOP languages, just with a lot more boilerplate. With that said, this snippet is in Scala:

```scala
// A binary operator is either addition, subtraction, multiplication, or division
abstract class BinOp
case object Add extends BinOp
case object Sub extends BinOp
case object Mul extends BinOp
case object Div extends BinOp

// An expression is either an atomic expression or a binary expression
abstract class Expr
case class AtomicExpr(value: Int) extends Expr
case class BinaryExpr(op: BinOp, lhs: Expr, rhs: Expr) extends Expr

object Main extends App {
  def eval(expr: Expr): Int = {
    expr match {
      // if an expression is just an atomic expression,
      // it evaluates to the contained value
      case AtomicExpr(value) => value

      // if an expression is a binary expression, run
      // the operator on the two arguments
      case BinaryExpr(op, lhs, rhs) =>
        op match {
          case Add => eval(lhs) + eval(rhs)
          case Sub => eval(lhs) - eval(rhs)
          case Mul => eval(lhs) * eval(rhs)
          case Div => eval(lhs) / eval(rhs)
        }
    }
  }

  println(eval(BinaryExpr(Add, AtomicExpr(1), AtomicExpr(2)))) // 3
}
```

It's fairly straightforward, and this example is pretty limited, but the core concepts used here can be easily expanded to Turing-completeness pretty simply. In expression based languages, this is the core component of the AST; the whole program can be run as one call to `eval()`.
