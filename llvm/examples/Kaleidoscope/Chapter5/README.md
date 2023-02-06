```C++
// Emit then value.
Builder.SetInsertPoint(ThenBB);

Value *ThenV = Then->codegen();
if (!ThenV)
  return nullptr;

Builder.CreateBr(MergeBB);
// Codegen of 'Then' can change the current block, update ThenBB for the PHI.
ThenBB = Builder.GetInsertBlock();
```

Once the insertion point is set, we recursively codegen the “then” expression from the AST. To finish off the “then”
block, we create an unconditional branch to the merge block. One interesting (and very important) aspect of the LLVM IR
is that it requires all basic blocks to be “terminated” with a control flow instruction such as return or branch. This
means that all control flow, including fall throughs must be made explicit in the LLVM IR. If you violate this rule, the
verifier will emit an error.

```C
// Within the loop, the variable is defined equal to the PHI node.  If it
// shadows an existing variable, we have to restore it, so save it now.
Value *OldVal = NamedValues[VarName];
NamedValues[VarName] = Variable;

// Emit the body of the loop.  This, like any other expr, can change the
// current BB.  Note that we ignore the value computed by the body, but don't
// allow an error.
if (!Body->codegen())
  return nullptr;
```

Now the code starts to get more interesting. Our ‘for’ loop introduces a new variable to the symbol table. This means
that our symbol table can now contain either function arguments or loop variables. To handle this, before we codegen the
body of the loop, we add the loop variable as the current value for its name. Note that it is possible that there is a
variable of the same name in the outer scope. It would be easy to make this an error (emit an error and return null if
there is already an entry for VarName) but we choose to allow shadowing of variables. In order to handle this correctly,
we remember the Value that we are potentially shadowing in OldVal (which will be null if there is no shadowed variable).