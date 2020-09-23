---
title: [solved] src/lib9/fmt/fltfmt.c: In function ‘__efgfmt’
date: 2018-07-06 18:26:00
updated: 2018-12-27 08:53:07
tags: 
- vscode
- vue
categories: 
- js

---
笔者在编译go1.4.3时报错。
```bash
./all.bash 
./all.bash# Building C bootstrap tool.
cmd/dist

# Building compilers and Go bootstrap tool for host, linux/amd64.
lib9
/home/kelvin/go/src/lib9/fmt/fltfmt.c: In function ‘__efgfmt’:
/home/kelvin/go/src/lib9/fmt/fltfmt.c:437:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ndigits > prec) {
     ^
/home/kelvin/go/src/lib9/fmt/fltfmt.c:451:2: note: here
  default:
  ^~~~~~~

<!--more-->

/home/kelvin/go/src/lib9/fmt/strtod.c: In function ‘fmtstrtod’:
/home/kelvin/go/src/lib9/fmt/strtod.c:141:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(state == S0)
      ^
/home/kelvin/go/src/lib9/fmt/strtod.c:145:3: note: here
   case '+':
   ^~~~
/home/kelvin/go/src/lib9/fmt/strtod.c:182:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(xcmp(s, "nan") == 0) {
     ^
/home/kelvin/go/src/lib9/fmt/strtod.c:187:2: note: here
  case S1:
  ^~~~
/home/kelvin/go/src/lib9/fmt/strtod.c:193:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(xcmp(s, "inf") == 0) {
     ^
/home/kelvin/go/src/lib9/fmt/strtod.c:198:2: note: here
  case S3:
  ^~~~
/home/kelvin/go/src/lib9/fmt/strtod.c:203:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
   s--;  /* back over +- */
   ~^~
/home/kelvin/go/src/lib9/fmt/strtod.c:204:2: note: here
  case S5:
  ^~~~
libbio
/home/kelvin/go/src/libbio/bflush.c: In function ‘Bflush’:
/home/kelvin/go/src/libbio/bflush.c:51:13: warning: this statement may fall through [-Wimplicit-fallthrough=]
   bp->state = Bractive;
   ~~~~~~~~~~^~~~~~~~~~
/home/kelvin/go/src/libbio/bflush.c:53:2: note: here
  case Bractive:
  ^~~~
/home/kelvin/go/src/libbio/bseek.c: In function ‘Bseek’:
/home/kelvin/go/src/libbio/bseek.c:51:12: warning: this statement may fall through [-Wimplicit-fallthrough=]
   bp->gbuf = bp->ebuf;
   ~~~~~~~~~^~~~~~~~~~
/home/kelvin/go/src/libbio/bseek.c:53:2: note: here
  case Bractive:
  ^~~~
liblink
/home/kelvin/go/src/liblink/asm5.c: In function ‘buildop’:
/home/kelvin/go/src/liblink/asm5.c:1352:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("bad code");
    ^~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm5.c:1353:3: note: here
   case AADD:
   ^~~~
/home/kelvin/go/src/liblink/asm5.c: In function ‘ofsr’:
/home/kelvin/go/src/liblink/asm5.c:2617:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   ctxt->diag("bad fst %A", a);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm5.c:2618:2: note: here
  case AMOVD:
  ^~~~
/home/kelvin/go/src/liblink/asm6.c: In function ‘doasm’:
/home/kelvin/go/src/liblink/asm6.c:3378:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown TLS base location for %s", headstr(ctxt->headtype));
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm6.c:3380:3: note: here
   case Hplan9:
   ^~~~
/home/kelvin/go/src/liblink/asm6.c: In function ‘prefixof’:
/home/kelvin/go/src/liblink/asm6.c:1909:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown TLS base register for %s", headstr(ctxt->headtype));
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm6.c:1910:3: note: here
   case Hdragonfly:
   ^~~~
/home/kelvin/go/src/liblink/asm6.c: In function ‘oclass’:
/home/kelvin/go/src/liblink/asm6.c:1983:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ctxt->asmode != 64)
     ^
/home/kelvin/go/src/liblink/asm6.c:1985:2: note: here
  case D_DL:
  ^~~~
/home/kelvin/go/src/liblink/asm6.c:2011:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ctxt->asmode != 64)
     ^
/home/kelvin/go/src/liblink/asm6.c:2013:2: note: here
  case D_SP:
  ^~~~
/home/kelvin/go/src/liblink/asm6.c: In function ‘mediaop’:
/home/kelvin/go/src/liblink/asm6.c:2695:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(osize != 1){
     ^
/home/kelvin/go/src/liblink/asm6.c:2702:2: note: here
  default:
  ^~~~~~~
/home/kelvin/go/src/liblink/asm6.c: In function ‘asmidx’:
/home/kelvin/go/src/liblink/asm6.c:2156:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ctxt->asmode != 64)
     ^
/home/kelvin/go/src/liblink/asm6.c:2158:2: note: here
  case D_AX:
  ^~~~
/home/kelvin/go/src/liblink/asm6.c:2198:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ctxt->asmode != 64)
     ^
/home/kelvin/go/src/liblink/asm6.c:2200:2: note: here
  case D_AX:
  ^~~~
/home/kelvin/go/src/liblink/asm8.c: In function ‘doasm’:
/home/kelvin/go/src/liblink/asm8.c:2694:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown TLS base location for %s", headstr(ctxt->headtype));
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm8.c:2696:3: note: here
   case Hlinux:
   ^~~~
/home/kelvin/go/src/liblink/asm8.c: In function ‘prefixof’:
/home/kelvin/go/src/liblink/asm8.c:1466:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown TLS base register for %s", headstr(ctxt->headtype));
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/asm8.c:1467:3: note: here
   case Hdarwin:
   ^~~~
/home/kelvin/go/src/liblink/asm8.c: In function ‘mediaop’:
/home/kelvin/go/src/liblink/asm8.c:2131:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(osize != 1){
     ^
/home/kelvin/go/src/liblink/asm8.c:2138:2: note: here
  default:
  ^~~~~~~
/home/kelvin/go/src/liblink/obj5.c: In function ‘addstacksplit’:
/home/kelvin/go/src/liblink/obj5.c:383:23: warning: this statement may fall through [-Wimplicit-fallthrough=]
    cursym->text->mark &= ~LEAF;
    ~~~~~~~~~~~~~~~~~~~^~~~~~~~
/home/kelvin/go/src/liblink/obj5.c:385:3: note: here
   case ABCASE:
   ^~~~
/home/kelvin/go/src/liblink/sym.c: In function ‘linknew’:
/home/kelvin/go/src/liblink/sym.c:150:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown thread-local storage offset for nacl/%s", ctxt->arch->name);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/sym.c:151:3: note: here
   case '6':
   ^~~~
/home/kelvin/go/src/liblink/sym.c:170:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    sysfatal("unknown thread-local storage offset for darwin/%s", ctxt->arch->name);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/liblink/sym.c:171:3: note: here
   case '6':
   ^~~~
cmd/cc
/home/kelvin/go/src/cmd/cc/com64.c: In function ‘com64’:
/home/kelvin/go/src/cmd/cc/com64.c:257:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(rv) {
      ^
/home/kelvin/go/src/cmd/cc/com64.c:265:3: note: here
   case OCOND:
   ^~~~
/home/kelvin/go/src/cmd/cc/com64.c:300:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    diag(n, "unknown vlong %O", n->op);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/cc/com64.c:301:3: note: here
   case OFUNC:
   ^~~~
/home/kelvin/go/src/cmd/cc/com64.c:302:15: warning: this statement may fall through [-Wimplicit-fallthrough=]
    n->complex = FNX;
               ^
/home/kelvin/go/src/cmd/cc/com64.c:303:3: note: here
   case ORETURN:
   ^~~~
/home/kelvin/go/src/cmd/cc/acid.c: In function ‘acidmember’:
/home/kelvin/go/src/cmd/cc/acid.c:150:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(flag) {
     ^
/home/kelvin/go/src/cmd/cc/acid.c:177:2: note: here
  case TINT:
  ^~~~
/home/kelvin/go/src/cmd/cc/dpchk.c: In function ‘getflag’:
/home/kelvin/go/src/cmd/cc/dpchk.c:121:9: warning: this statement may fall through [-Wimplicit-fallthrough=]
    nstar++;
    ~~~~~^~
/home/kelvin/go/src/cmd/cc/dpchk.c:122:3: note: here
   case Fignor:
   ^~~~
/home/kelvin/go/src/cmd/cc/dcl.c: In function ‘isstruct’:
/home/kelvin/go/src/cmd/cc/dcl.c:295:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(n && n->type && sametype(n->type, t))
     ^
/home/kelvin/go/src/cmd/cc/dcl.c:297:2: note: here
  case OSTRING:
  ^~~~
/home/kelvin/go/src/cmd/cc/lex.c: In function ‘compile’:
/home/kelvin/go/src/cmd/cc/lex.c:308:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    errorexit();
    ^~~~~~~~~~~
/home/kelvin/go/src/cmd/cc/lex.c:309:3: note: here
   case 0:
   ^~~~
/home/kelvin/go/src/cmd/cc/lex.c:338:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    errorexit();
    ^~~~~~~~~~~
/home/kelvin/go/src/cmd/cc/lex.c:339:3: note: here
   default:
   ^~~~~~~
/home/kelvin/go/src/cmd/cc/sub.c: In function ‘allfloat’:
/home/kelvin/go/src/cmd/cc/sub.c:528:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(!allfloat(n->right, flag))
      ^
/home/kelvin/go/src/cmd/cc/sub.c:530:3: note: here
   case OCAST:
   ^~~~
/home/kelvin/go/src/cmd/cc/sub.c: In function ‘simplifyshift’:
/home/kelvin/go/src/cmd/cc/sub.c:863:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(topbit(c3) >= (32-c2))
     ^
/home/kelvin/go/src/cmd/cc/sub.c:865:2: note: here
  case 001: /* (((e >>u c2) & c3) <<u c1) */
  ^~~~
/home/kelvin/go/src/cmd/cc/sub.c:880:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(c2 <= 0)
     ^
/home/kelvin/go/src/cmd/cc/sub.c:882:2: note: here
  case 012: /* (((e >>s c2) & c3) >>u c1) */
  ^~~~
/home/kelvin/go/src/cmd/cc/sub.c:900:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(topbit(c3) >= 31)
     ^
/home/kelvin/go/src/cmd/cc/sub.c:902:2: note: here
  case 010: /* (((e <<u c2) & c3) >>u c1) */
  ^~~~
/home/kelvin/go/src/cmd/cc/sub.c: In function ‘side’:
/home/kelvin/go/src/cmd/cc/sub.c:954:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   n = n->right;
   ~~^~~~~~~~~~
/home/kelvin/go/src/cmd/cc/sub.c:956:2: note: here
  case OEQ:
  ^~~~
/home/kelvin/go/src/cmd/cc/sub.c: In function ‘relindex’:
/home/kelvin/go/src/cmd/cc/sub.c:1130:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   diag(Z, "bad in relindex: %O", o);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/cc/sub.c:1131:2: note: here
  case OEQ: return 0;
  ^~~~
cmd/gc
/home/kelvin/go/src/cmd/gc/const.c: In function ‘convlit1’:
/home/kelvin/go/src/cmd/gc/const.c:224:12: warning: this statement may fall through [-Wimplicit-fallthrough=]
     n->val = toint(n->val);
     ~~~~~~~^~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/const.c:226:4: note: here
    case CTINT:
    ^~~~
/home/kelvin/go/src/cmd/gc/const.c:238:12: warning: this statement may fall through [-Wimplicit-fallthrough=]
     n->val = toflt(n->val);
     ~~~~~~~^~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/const.c:240:4: note: here
    case CTFLT:
    ^~~~
/home/kelvin/go/src/cmd/gc/const.c: In function ‘tostr’:
/home/kelvin/go/src/cmd/gc/const.c:470:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   yyerror("no float -> string");
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/const.c:472:2: note: here
  case CTNIL:
  ^~~~
/home/kelvin/go/src/cmd/gc/const.c: In function ‘nodlit’:
/home/kelvin/go/src/cmd/gc/const.c:1050:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("nodlit ctype %d", v.ctype);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/const.c:1051:2: note: here
  case CTSTR:
  ^~~~
/home/kelvin/go/src/cmd/gc/const.c: In function ‘convconst’:
/home/kelvin/go/src/cmd/gc/const.c:1427:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    fatal("convconst ctype=%d %lT", val->ctype, t);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/const.c:1428:3: note: here
   case CTINT:
   ^~~~
/home/kelvin/go/src/cmd/gc/esc.c: In function ‘esccall’:
/home/kelvin/go/src/cmd/gc/esc.c:898:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("esccall");
   ^~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/esc.c:900:2: note: here
  case OCALLFUNC:
  ^~~~
/home/kelvin/go/src/cmd/gc/fmt.c: In function ‘typefmt’:
/home/kelvin/go/src/cmd/gc/fmt.c:624:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(fmtmode != FExp) {
      ^
/home/kelvin/go/src/cmd/gc/fmt.c:628:3: note: here
   default:
   ^~~~~~~
/home/kelvin/go/src/cmd/gc/gen.c: In function ‘cgen_proc’:
/home/kelvin/go/src/cmd/gc/gen.c:550:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("cgen_proc: unknown call %O", n->left->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/gen.c:552:2: note: here
  case OCALLMETH:
  ^~~~
/home/kelvin/go/src/cmd/gc/lex.c: In function ‘ccgetc’:
/home/kelvin/go/src/cmd/gc/lex.c:1679:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(curio.bin != nil) {
     ^
/home/kelvin/go/src/cmd/gc/lex.c:1683:2: note: here
  case EOF:
  ^~~~
/home/kelvin/go/src/cmd/gc/lex.c:1688:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   c = '\n';
   ~~^~~~~~
/home/kelvin/go/src/cmd/gc/lex.c:1689:2: note: here
  case '\n':
  ^~~~
/home/kelvin/go/src/cmd/gc/mparith1.c: In function ‘mpatoflt’:
/home/kelvin/go/src/cmd/gc/mparith1.c:352:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    f = 1;
    ~~^~~
/home/kelvin/go/src/cmd/gc/mparith1.c:354:3: note: here
   case ' ':
   ^~~~
/home/kelvin/go/src/cmd/gc/mparith1.c:383:7: warning: this statement may fall through [-Wimplicit-fallthrough=]
    eb = 1;
    ~~~^~~
/home/kelvin/go/src/cmd/gc/mparith1.c:385:3: note: here
   case 'E':
   ^~~~
/home/kelvin/go/src/cmd/gc/mparith1.c: In function ‘mpatofix’:
/home/kelvin/go/src/cmd/gc/mparith1.c:468:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   f = 1;
   ~~^~~
/home/kelvin/go/src/cmd/gc/mparith1.c:470:2: note: here
  case '+':
  ^~~~
/home/kelvin/go/src/cmd/gc/order.c: In function ‘orderstmt’:
/home/kelvin/go/src/cmd/gc/order.c:732:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    fatal("orderstmt range %T", n->type);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/order.c:733:3: note: here
   case TARRAY:
   ^~~~
/home/kelvin/go/src/cmd/gc/order.c:511:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("orderstmt %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/order.c:513:2: note: here
  case OVARKILL:
  ^~~~
/home/kelvin/go/src/cmd/gc/order.c: In function ‘ordersafeexpr’:
/home/kelvin/go/src/cmd/gc/order.c:141:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("ordersafeexpr %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/order.c:143:2: note: here
  case ONAME:
  ^~~~
/home/kelvin/go/src/cmd/gc/order.c: In function ‘ordermapassign’:
/home/kelvin/go/src/cmd/gc/order.c:453:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("ordermapassign %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/order.c:455:2: note: here
  case OAS:
  ^~~~
/home/kelvin/go/src/cmd/gc/racewalk.c: In function ‘racewalknode’:
/home/kelvin/go/src/cmd/gc/racewalk.c:156:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("racewalk: unknown node type %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/racewalk.c:158:2: note: here
  case OASOP:
  ^~~~
/home/kelvin/go/src/cmd/gc/range.c: In function ‘walkrange’:
/home/kelvin/go/src/cmd/gc/range.c:144:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("walkrange");
   ^~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/range.c:146:2: note: here
  case TARRAY:
  ^~~~
/home/kelvin/go/src/cmd/gc/select.c: In function ‘walkselect’:
/home/kelvin/go/src/cmd/gc/select.c:128:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
     fatal("select %O", n->op);
     ^~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/select.c:130:4: note: here
    case OSEND:
    ^~~~
/home/kelvin/go/src/cmd/gc/select.c:223:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    fatal("select %O", n->op);
    ^~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/select.c:225:3: note: here
   case OSEND:
   ^~~~
/home/kelvin/go/src/cmd/gc/select.c:289:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
     fatal("select %O", n->op);
     ^~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/select.c:291:4: note: here
    case OSEND:
    ^~~~
/home/kelvin/go/src/cmd/gc/sinit.c: In function ‘initplan’:
/home/kelvin/go/src/cmd/gc/sinit.c:1375:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("initplan");
   ^~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/sinit.c:1376:2: note: here
  case OARRAYLIT:
  ^~~~
/home/kelvin/go/src/cmd/gc/sinit.c: In function ‘anylit’:
/home/kelvin/go/src/cmd/gc/sinit.c:1040:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("anylit: not lit");
   ^~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/sinit.c:1042:2: note: here
  case OPTRLIT:
  ^~~~
/home/kelvin/go/src/cmd/gc/sinit.c: In function ‘iszero’:
/home/kelvin/go/src/cmd/gc/sinit.c:1451:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    fatal("iszero");
    ^~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/sinit.c:1453:3: note: here
   case CTNIL:
   ^~~~
/home/kelvin/go/src/cmd/gc/subr.c: In function ‘assignop’:
/home/kelvin/go/src/cmd/gc/subr.c:1291:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(dst->bound != -100) // not slice
      ^
/home/kelvin/go/src/cmd/gc/subr.c:1293:3: note: here
   case TPTR32:
   ^~~~
/home/kelvin/go/src/cmd/gc/subr.c: In function ‘genhash’:
/home/kelvin/go/src/cmd/gc/subr.c:2745:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("genhash %T", t);
   ^~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/subr.c:2746:2: note: here
  case TARRAY:
  ^~~~
/home/kelvin/go/src/cmd/gc/subr.c: In function ‘geneq’:
/home/kelvin/go/src/cmd/gc/subr.c:2985:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("geneq %T", t);
   ^~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/subr.c:2986:2: note: here
  case TARRAY:
  ^~~~
/home/kelvin/go/src/cmd/gc/typecheck.c: In function ‘typecheck1’:
/home/kelvin/go/src/cmd/gc/typecheck.c:336:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("typecheck %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/typecheck.c:341:2: note: here
  case OLITERAL:
  ^~~~
/home/kelvin/go/src/cmd/gc/walk.c: In function ‘walkstmt’:
/home/kelvin/go/src/cmd/gc/walk.c:220:9: warning: this statement may fall through [-Wimplicit-fallthrough=]
   n->op = OCASE;
   ~~~~~~^~~~~~~
/home/kelvin/go/src/cmd/gc/walk.c:221:2: note: here
  case OCASE:
  ^~~~
/home/kelvin/go/src/cmd/gc/walk.c: In function ‘walkexpr’:
/home/kelvin/go/src/cmd/gc/walk.c:1128:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("walkexpr ORECV"); // should see inside OAS only
   ^~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/gc/walk.c:1130:2: note: here
  case OSLICE:
  ^~~~
cmd/6l
/home/kelvin/go/src/cmd/6l/../ld/data.c: In function ‘relocsym’:
/home/kelvin/go/src/cmd/6l/../ld/data.c:315:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    diag("bad reloc size %#ux for %s", siz, r->sym->name);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/data.c:316:3: note: here
   case 1:
   ^~~~
/home/kelvin/go/src/cmd/6l/../ld/elf.c: In function ‘asmbelf’:
/home/kelvin/go/src/cmd/6l/../ld/elf.c:1141:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   errorexit();
   ^~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/elf.c:1142:2: note: here
  case '5':
  ^~~~
/home/kelvin/go/src/cmd/6l/../ld/ldelf.c: In function ‘reltype’:
/home/kelvin/go/src/cmd/6l/../ld/ldelf.c:875:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   diag("%s: unknown relocation type %d; compiled without -fpic?", pn, elftype);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/ldelf.c:876:2: note: here
  case R('5', R_ARM_ABS32):
  ^~~~
/home/kelvin/go/src/cmd/6l/../ld/ldpe.c: In function ‘ldpe’:
/home/kelvin/go/src/cmd/6l/../ld/ldpe.c:298:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
      diag("%s: unknown relocation type %d;", pn, type);
      ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/ldpe.c:299:5: note: here
     case IMAGE_REL_I386_REL32:
     ^~~~
/home/kelvin/go/src/cmd/6l/../ld/macho.c: In function ‘asmbmacho’:
/home/kelvin/go/src/cmd/6l/../ld/macho.c:358:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   errorexit();
   ^~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/macho.c:359:2: note: here
  case '6':
  ^~~~
/home/kelvin/go/src/cmd/6l/../ld/macho.c:417:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    errorexit();
    ^~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/macho.c:418:3: note: here
   case '6':
   ^~~~
/home/kelvin/go/src/cmd/6l/../ld/pe.c: In function ‘asmbpe’:
/home/kelvin/go/src/cmd/6l/../ld/pe.c:617:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   errorexit();
   ^~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/../ld/pe.c:618:2: note: here
  case '6':
  ^~~~
/home/kelvin/go/src/cmd/6l/../ld/symtab.c: In function ‘putplan9sym’:
/home/kelvin/go/src/cmd/6l/../ld/symtab.c:246:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(ver)
     ^
/home/kelvin/go/src/cmd/6l/../ld/symtab.c:248:2: note: here
  case 'a':
  ^~~~
/home/kelvin/go/src/cmd/6l/obj.c: In function ‘archinit’:
/home/kelvin/go/src/cmd/6l/obj.c:86:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   errorexit();
   ^~~~~~~~~~~
/home/kelvin/go/src/cmd/6l/obj.c:87:2: note: here
  case Hplan9:  /* plan 9 */
  ^~~~
cmd/6a
cmd/6c
/home/kelvin/go/src/cmd/6c/peep.c: In function ‘peep’:
/home/kelvin/go/src/cmd/6c/peep.c:111:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
    t++;
    ~^~
/home/kelvin/go/src/cmd/6c/peep.c:113:3: note: here
   case ADATA:
   ^~~~
/home/kelvin/go/src/cmd/6c/peep.c: In function ‘copyu’:
/home/kelvin/go/src/cmd/6c/peep.c:573:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(copyas(&p->from, v))
     ^
/home/kelvin/go/src/cmd/6c/peep.c:577:2: note: here
  case ANOP: /* rhs store */
  ^~~~
/home/kelvin/go/src/cmd/6c/peep.c:747:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(p->to.type != D_NONE) {
     ^
/home/kelvin/go/src/cmd/6c/peep.c:753:2: note: here
  case ADIVB:
  ^~~~
/home/kelvin/go/src/cmd/6c/reg.c: In function ‘regopt’:
/home/kelvin/go/src/cmd/6c/reg.c:340:6: warning: this statement may fall through [-Wimplicit-fallthrough=]
    if(p->to.type != D_NONE)
      ^
/home/kelvin/go/src/cmd/6c/reg.c:343:3: note: here
   case AIDIVB:
   ^~~~
/home/kelvin/go/src/cmd/6c/txt.c: In function ‘gmove’:
/home/kelvin/go/src/cmd/6c/txt.c:995:28: warning: left shift of negative value [-Wshift-negative-value]
     f->vconst |= (vlong)~0 << 32;
                            ^~
/home/kelvin/go/src/cmd/6c/txt.c:1045:28: warning: left shift of negative value [-Wshift-negative-value]
     f->vconst |= (vlong)~0 << 32;
                            ^~
/home/kelvin/go/src/cmd/6c/txt.c:762:5: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(typefd[tt]) {
     ^
/home/kelvin/go/src/cmd/6c/txt.c:773:2: note: here
  case TUVLONG:
  ^~~~
cmd/6g
/home/kelvin/go/src/cmd/6g/../gc/cplx.c: In function ‘complexmove’:
/home/kelvin/go/src/cmd/6g/../gc/cplx.c:54:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("complexmove: unknown conversion: %T -> %T\n",
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    f->type, t->type);
    ~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/../gc/cplx.c:57:2: note: here
  case CASE(TCOMPLEX64,TCOMPLEX64):
  ^~~~
/home/kelvin/go/src/cmd/6g/../gc/cplx.c: In function ‘complexgen’:
/home/kelvin/go/src/cmd/6g/../gc/cplx.c:199:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("complexgen: unknown op %O", n->op);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/../gc/cplx.c:201:2: note: here
  case ODOT:
  ^~~~
/home/kelvin/go/src/cmd/6g/cgen.c: In function ‘bgen’:
/home/kelvin/go/src/cmd/6g/cgen.c:1127:14: warning: this statement may fall through [-Wimplicit-fallthrough=]
   if(nr == N || nr->type == T)
/home/kelvin/go/src/cmd/6g/cgen.c:1130:2: note: here
  case ONOT: // unary
  ^~~~
/home/kelvin/go/src/cmd/6g/gsubr.c: In function ‘regalloc’:
/home/kelvin/go/src/cmd/6g/gsubr.c:373:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("out of fixed registers");
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:375:2: note: here
  case TFLOAT32:
  ^~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:385:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("out of floating registers");
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:387:2: note: here
  case TCOMPLEX64:
  ^~~~
/home/kelvin/go/src/cmd/6g/gsubr.c: In function ‘gmove’:
/home/kelvin/go/src/cmd/6g/gsubr.c:684:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("gmove %lT -> %lT", f->type, t->type);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:689:2: note: here
  case CASE(TINT8, TINT8): // same size
  ^~~~
/home/kelvin/go/src/cmd/6g/gsubr.c: In function ‘naddr’:
/home/kelvin/go/src/cmd/6g/gsubr.c:1215:4: warning: this statement may fall through [-Wimplicit-fallthrough=]
    fatal("naddr: ONAME class %S %d\n", n->sym, n->class);
    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:1216:3: note: here
   case PEXTERN:
   ^~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:1281:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("naddr: OADDR\n");
   ^~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/gsubr.c:1283:2: note: here
  case OITAB:
  ^~~~
/home/kelvin/go/src/cmd/6g/reg.c: In function ‘addmove’:
/home/kelvin/go/src/cmd/6g/reg.c:525:3: warning: this statement may fall through [-Wimplicit-fallthrough=]
   fatal("unknown type %E", v->etype);
   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/kelvin/go/src/cmd/6g/reg.c:526:2: note: here
  case TINT8:
  ^~~~
runtime
errors
sync/atomic
sync
io
unicode
unicode/utf8
unicode/utf16
bytes
math
strings
strconv
bufio
sort
container/heap
encoding/base64
syscall
time
os
reflect
fmt
encoding
encoding/json
flag
path/filepath
path
io/ioutil
log
regexp/syntax
regexp
go/token
go/scanner
go/ast
go/parser
os/exec
os/signal
net/url
text/template/parse
text/template
go/doc
go/build
cmd/go

# Building packages and commands for linux/amd64.
runtime
errors
sync/atomic
unicode
unicode/utf8
math
sort
sync
container/heap
encoding
unicode/utf16
io
syscall
hash
hash/crc32
container/list
crypto/subtle
crypto/cipher
bytes
strings
path
crypto/hmac
hash/adler32
container/ring
hash/crc64
bufio
time
text/tabwriter
internal/syscall
html
compress/bzip2
hash/fnv
image/color
runtime/race
image/color/palette
strconv
math/rand
os
math/cmplx
reflect
regexp/syntax
crypto
crypto/md5
encoding/base64
path/filepath
net/url
io/ioutil
os/exec
crypto/aes
crypto/rc4
fmt
encoding/binary
crypto/sha1
regexp
crypto/sha256
crypto/des
crypto/sha512
encoding/pem
os/signal
encoding/ascii85
encoding/base32
image
cmd/pprof/internal/svg
index/suffixarray
cmd/internal/goobj
cmd/internal/rsc.io/arm/armasm
cmd/internal/rsc.io/x86/x86asm
debug/dwarf
debug/gosym
debug/plan9obj
flag
debug/elf
debug/macho
debug/pe
log
go/token
encoding/json
encoding/xml
cmd/internal/objfile
go/scanner
go/ast
cmd/addr2line
text/template/parse
compress/flate
go/parser
go/printer
compress/gzip
text/template
math/big
encoding/hex
cmd/cgo
go/format
cmd/fix
go/doc
crypto/elliptic
go/build
encoding/asn1
crypto/rand
crypto/rsa
crypto/ecdsa
crypto/dsa
crypto/x509/pkix
mime
net/http/internal
runtime/pprof
cmd/nm
cmd/gofmt
cmd/objdump
cmd/pack
runtime/cgo
cmd/pprof/internal/profile
html/template
cmd/pprof/internal/tempfile
cmd/yacc
cmd/pprof/internal/plugin
cmd/pprof/internal/symbolizer
cmd/pprof/internal/symbolz
archive/tar
cmd/pprof/internal/report
archive/zip
# runtime/cgo
/usr/bin/x86_64-linux-gnu-ld: -r and -pie may not be used together
collect2: error: ld returned 1 exit status
compress/lzw
compress/zlib
database/sql/driver
encoding/csv
encoding/gob
database/sql
image/draw
cmd/pprof/internal/commands
image/gif
cmd/pprof/internal/driver
image/jpeg
image/png
runtime/debug
testing
testing/iotest
testing/quick
text/scanner
```
设置:
```bash
export CC="gcc -Wimplicit-fallthrough=0 -Wno-error=shift-negative-value -Wno-shift-negative-value" 
```
