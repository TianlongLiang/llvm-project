```sh
ready> extern sin(x);
Read extern:
declare double @sin(double)

ready> extern cos(x);
Read extern:
declare double @cos(double)

ready> sin(1.0);
Read top-level expression:
define double @2() {
entry:
  ret double 0x3FEAED548F090CEE
}

Evaluated to 0.841471

ready> def foo(x) sin(x)*sin(x) + cos(x)*cos(x);
Read function definition:
define double @foo(double %x) {
entry:
  %calltmp = call double @sin(double %x)
  %multmp = fmul double %calltmp, %calltmp
  %calltmp2 = call double @cos(double %x)
  %multmp4 = fmul double %calltmp2, %calltmp2
  %addtmp = fadd double %multmp, %multmp4
  ret double %addtmp
}

ready> foo(4.0);
Read top-level expression:
define double @3() {
entry:
  %calltmp = call double @foo(double 4.000000e+00)
  ret double %calltmp
}

Evaluated to 1.000000

```

how does the JIT know about sin and cos? The answer is surprisingly simple: The KaleidoscopeJIT has a straightforward symbol resolution rule that it uses to find symbols that aren’t available in any given module:

**First** it searches **all the modules** that have already been added to the JIT, from the **most recent to the oldest**, to find the newest definition.

**If no definition** is found inside the JIT, it falls back to calling “dlsym("sin")” on the Kaleidoscope
process itself. Since “sin” is defined within the JIT’s address space, it simply patches up calls in the module to call
the libm version of sin directly. But in some cases this even goes further: as sin and cos are names of standard math
functions, the constant folder will directly evaluate the function calls to the correct result when called with
constants like in the “sin(1.0)” above.