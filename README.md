# singleton-ops

[![Join the chat at https://gitter.im/fthomas/singleton-ops](https://badges.gitter.im/fthomas/singleton-ops.svg)](https://gitter.im/fthomas/singleton-ops?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![Build Status](https://travis-ci.org/fthomas/singleton-ops.svg?branch=master)](https://travis-ci.org/fthomas/singleton-ops)
[![codecov](https://codecov.io/gh/fthomas/singleton-ops/branch/master/graph/badge.svg)](https://codecov.io/gh/fthomas/singleton-ops)

This library provides type-level operations for [Typelevel Scala][typelevel-scala] with [SIP-23][sip-23].

####Supported types:
* `Int` 
* `Long`
* `Double`
* `String`
* `Boolean`
* `Nat` (from the Shapeless library)

####Supported arithmetic operations:
* `type +[P1, P2]`          
* `type -[P1, P2]`          
* `type *[P1, P2]`          
* `type /[P1, P2]`          
* `type Abs[P1]`            
* `type Negate[P1]`         

####Supported logical operations:
* `type ![P1]`              
* `type <[P1, P2]`          
* `type <=[P1, P2]`         
* `type >=[P1, P2]`         
* `type >[P1, P2]`          
* `type ==[P1, P2]`         
* `type !=[P1, P2]`         
* `type &&[P1, P2]`         
* `type ||[P1, P2]`         
* `type Min[P1, P2]`        
* `type Max[P1, P2]`        

####Supported explicit conversion operations:
* `type ToNat[P1]`          
* `type ToInt[P1]`          
* `type ToLong[P1]`         
* `type ToDouble[P1]`       

####Supported string operations:
* `type +[P1, P2]` (concat)          
* `type Reverse[P1]`        
* `type Substring[P1, P2]`  

####Supported constraints operations:
* `type Require[P1]`        

## Examples

* `Int` type operations:
```scala
import singleton.ops._
def demo[L <: Int with Singleton](implicit p : L*L + L) : p.Out = p.value
val b : 30 = demo[5]
```
* `Long` type operations:
```scala
import singleton.ops._
def demoLong[L1 <: Long with Singleton, L2 <: Long with Singleton](implicit p : Min[L1*L1, L2+L2]) : p.Out = p.value
val bLong1 : 1L = demoLong[1L, 5L]
val bLong2 : 6L = demoLong[3L, 3L]
```

* `Double` type operations:
```scala
import singleton.ops._
def demoDouble[L1 <: Double with Singleton, L2 <: Double with Singleton](implicit p : L1 / L2 + 1.0) : p.Out = p.value
val bDouble : 1.2 = demoDouble[1.0, 5.0]
```

* Combined `Long` and `Int` type operations:
```scala
import singleton.ops._
def demoSumLongInt[L1 <: Long with Singleton, L2 <: Int with Singleton](implicit p : L1 + L2) : p.Out = p.value
val bSumLongInt : 16L = demoSumLongInt[8L, 8]
```

* `String` type operations:
```scala
import singleton.ops._
def demoString[P1 <: String with Singleton](implicit op : Reverse[P1] + P1) : op.Out{} = op.value
val bString : "cbaabc" = demoString["abc"]
```

* `Boolean` type operations:
```scala
import singleton.ops._
def demoBoolean[P1 <: Int with Singleton](implicit op : P1 < 0) : op.Out{} = op.value
val bBoolean1 : true = demoBoolean[-5]
val bBoolean2 : false = demoBoolean[5]
val bBoolean3 : false = demoBoolean[0]
```

* `Boolean` type constraints:
```scala
import singleton.ops._
def demoRequire[P1 <: Int with Singleton](implicit op : Require[P1 < 0]) : op.Out{} = op.value
scala> demoRequire[-1]
demoRequire[-1]
res0: Boolean(true) = true
scala> demoRequire[1]
<console>:16: error: could not find implicit value for parameter op: singleton.ops.Require[singleton.ops.<[1,0]]
       demoRequire[1]
```

* Shapeless' `Nat` type operations:
```scala
import singleton.ops._
import shapeless._
val n = Nat(5)
//Converting Nat to Int singleton occurs implicitly
def demoNatToSing[L <: Nat](implicit p : L+L) : p.Out {} = p.value
val bSing10 : 10 = demoNatToSing[n.N]
//Converting Int singleton to Nat requires explicit `ToNat`
def demoSingToNat[L <: Int with Singleton](implicit op : ToNat[L+L]) : op.Out = op.value
val bNat10 : shapeless.nat._10 = demoSingToNat[5]
```

* Working with large numbers doesn't slay the compiler:
```scala
import singleton.ops._
def bigMul[L1 <: Long with Singleton, L2 <: Long with Singleton](implicit p : L1 * L2) : p.Out = p.value
scala> bigMul[32000L, 6400000L]
res2: Long = 204800000000
```

[shapeless]: https://github.com/milessabin/shapeless
[sip-23]: http://docs.scala-lang.org/sips/pending/42.type.html
[typelevel-scala]: https://github.com/typelevel/scala
