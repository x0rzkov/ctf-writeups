sqrt writeup
============

In this challenge we had to find the (2^64)th root of `5602276430032875007249509644314357293319755912603737631044802989314683039473469151600643674831915676677562504743413434940280819915470852112137937963496770923674944514657123370759858913638782767380945111493317828235741160391407042689991007589804877919105123960837253705596164618906554015382923343311865102111160 mod 6722156186149423473586056936189163112345526308304739592548269432948561498704906497631759731744824085311511299618196491816929603296108414569727189748975204102209646335725406551943711581704258725226874414399572244863268492324353927787818836752142254189928999592648333789131233670456465647924867060170327150559233`. This seems easy - plug in to `nth_root` on sage, which gave me the value `1948865039294009691576181380771672389220382961994854292305692557649261763833149884145614983319207887860531232498119502026176334583810204964826290882842308810728384018930976243008464049012096415817825074466275128141940107121005470692979995184344972514864128534992403176506223940852066206954491827309484962494271`, but does not work because there are actually 1073741824 64th roots of this number (because there are gcd(2^64, p-1) roots of unity). However, we may simply calculate a single root of unity (sage gave me `1605066445437061820619042845424639636743293247351002626223738461046315909063797675817757955661671001179615545926369278388014307925273091832457702418550896516819171654641394350435766368422984390199761844369743142130713167161790056530048501074195860170162224649096940784937558773388919320507953344332937322775474`), and then compute successive powers of this and multiply with our base root guess. We look for a short value because almost all values will be large, except the correct flag value.

Solve script:

```python
def encrypt(m, k, p):
    return pow(m, 1 << k, p)


#assert flag.startswith("TWCTF{")
#assert len(flag) == 42
#assert isPrime(p)

p = 6722156186149423473586056936189163112345526308304739592548269432948561498704906497631759731744824085311511299618196491816929603296108414569727189748975204102209646335725406551943711581704258725226874414399572244863268492324353927787818836752142254189928999592648333789131233670456465647924867060170327150559233
k = 64
pt = 1948865039294009691576181380771672389220382961994854292305692557649261763833149884145614983319207887860531232498119502026176334583810204964826290882842308810728384018930976243008464049012096415817825074466275128141940107121005470692979995184344972514864128534992403176506223940852066206954491827309484962494271
h = 1605066445437061820619042845424639636743293247351002626223738461046315909063797675817757955661671001179615545926369278388014307925273091832457702418550896516819171654641394350435766368422984390199761844369743142130713167161790056530048501074195860170162224649096940784937558773388919320507953344332937322775474

g = 3438332358968490881344155919145296251372757080629150991804744990816498140172070879978289432582589956941900786739276293096894841598246547366222142431811946062855877251326280341791697983338980798885639158045676757585753720032188339269806684469725099875846539885354821848894557007698661810185229270603897638348409
d = 1073741824
guess = pt
start = 0
guess = (guess * pow(h, start, p))%p
for t in xrange(0, d):
    guess = (guess*h)%p
    if guess < 10**106:
        print(guess)
        break
    if t%100000==0:
        print(t)
ct = encrypt(pt, k, p)

print(ct)
```

(This code takes over 600 million iterations to run, so takes ~15 minutes to run in pypy on my computer. For faster results change "start" value to 600000000).

Eventually it will print `46118657867175077974333582503480021752138026928648315026752964583384841511234055522567479767514232701`, which decodes to the flag.

Flag: `TWCTF{17s_v3ry_34sy_70_f1nd_th3_n_7h_r007}`