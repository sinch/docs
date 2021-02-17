---
title: SMPP Encoding
excerpt: ''
---
### Default SMSC encoding

<div class="magic-block-html">
    <div class="marked-table">
        <table>
            <thead valign="bottom">
            <tr class="row-odd">
                <th class="head" colspan="2">Data Coding</th>
            </tr>
            </thead>
            <tbody valign="top">
            <tr class="row-even">
                <td>0x00</td>
                <td>Default GSM 7 bit encoding</td>
            </tr>
            <tr class="row-odd">
                <td>0x01</td>
                <td>US-ASCII</td>
            </tr>
            <tr class="row-even">
                <td>0x02</td>
                <td>Binary</td>
            </tr>
            <tr class="row-odd">
                <td>0x03</td>
                <td>ISO8859-1 (Latin-1), only characters within GSM-7 character table can be parsed to handset</td>
            </tr>
            <tr class="row-even">
                <td>0x04</td>
                <td>Binary</td>
            </tr>
            <tr class="row-odd">
                <td>0x08</td>
                <td>UCS2/UTF-16BE</td>
            </tr>
            <tr class="row-even">
                <td>&gt;0xF0</td>
                <td>Same as DCS value. See SMPP v3.4 Issue1.2 specification</td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

### GSM 7 bit default alphabet and extension table

**3GPP TS 23.038 / GSM 03.38**

||||||||||||||||||
|------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
||x0 |x1 |x2 |x3 |x4 |x5 |x6 |x7 |x8 |x9 |xA |xB |xC |xD |xE |xF |
|0x    |@  |£  |$  |¥  |è  |é  |ù  |ì  |ò  |Ç  |LF |Ø  |ø  |CR |Å  |å  |
|1x    |Δ  |_  |Φ  |Γ  |Λ  |Ω  |Π  |Ψ  |Σ  |Θ  |Ξ  |ESC|Æ  |æ  |ß  |É  |
|2x    |SP |!  |“  |#  |¤  |%  |&  |‘  |(  |)  |*  |+  |,  |   |.  |/  |
|3x    |0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |:  |;  |<  |=  |>  |?  |
|4x    |¡  |A  |B  |C  |D  |E  |F  |G  |H  |I  |J  |K  |L  |M  |N  |O  |
|5x    |P  |Q  |R  |S  |T  |U  |V  |W  |X  |Y  |Z  |Ä  |Ö  |Ñ  |Ü  |§  |
|6x    |¿  |a  |b  |c  |d  |e  |f  |g  |h  |i  |j  |k  |l  |m  |n  |o  |
|7x    |p  |q  |r  |s  |t  |u  |v  |w  |x  |y  |z  |ä  |ö  |ñ  |ü  |à  |
|1B 0x |   |   |   |   |   |   |   |   |   |   |FF |   |   |   |   |   |
|1B 1x |   |   |   |   |^  |   |   |   |   |   |   |   |   |   |   |   |
|1B 2x |   |   |   |   |   |   |   |   |{  |}  |   |   |   |   |   |\  |
|1B 3x |   |   |   |   |   |   |   |   |   |   |   |   |[  |~  |]  |   |
|1B 4x |&#124; |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|1B 5x |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|1B 6x |   |   |   |   |   |€  |   |   |   |   |   |   |   |   |   |   |
|1B 7x |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |


### ISO/IEC 8859-1 table, Latin-1

**ISO/IEC8859-1**

||||||||||||||||||
|------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
||x0 |x1 |x2 |x3 |x4 |x5 |x6 |x7 |x8 |x9 |xA |xB |xC |xD |xE |xF |
|0x    |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|1x    |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|2x    |SP |!  |”  |#  |$  |%  |&  |‘  |(  |)  |*  |+  |,  |   |.  |/  |
|3x    |0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |:  |;  |<  |=  |>  |?  |
|4x    |@  |A  |B  |C  |D  |E  |F  |G  |H  |I  |J  |K  |L  |M  |N  |O  |
|5x    |P  |Q  |R  |S  |T  |U  |V  |W  |X  |Y  |Z  |[  |\  |]  |^  |_  |
|6x    |`  |a  |b  |c  |d  |e  |f  |g  |h  |i  |j  |k  |l  |m  |n  |o  |
|7x    |p  |q  |r  |s  |t  |u  |v  |w  |x  |y  |z  |{  |{  |}  |~  |   |
|8x    |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|9x    |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|Ax    |NBSP|¡  |¢  |£  |¤  |¥  |¦  |§  |¨  |©  |ª  |«  |¬  |SHY|®  |¯  |
|Bx    |°  |±  |²  |³  |´  |µ  |¶  |·  |¸  |¹  |º  |»  |¼  |½  |¾  |¿  |
|Cx    |À  |Á  |Â  |Ã  |Ä  |Å  |Æ  |Ç  |È  |É  |Ê  |Ë  |Ì  |Í  |Î  |Ï  |
|Dx    |Ð  |Ñ  |Ò  |Ó  |Ô  |Õ  |Ö  |×  |Ø  |Ù  |Ú  |Û  |Ü  |Ý  |Þ  |ß  |
|Ex    |à  |á  |â  |ã  |ä  |å  |æ  |ç  |è  |é  |ê  |ë  |ì  |í  |î  |ï  |
|Fx    |ð  |ñ  |ò  |ó  |ô  |õ  |ö  |÷  |ø  |ù  |ú  |û  |ü  |ý  |þ  |ÿ  |


### US-ASCII

||||||||||||||||||
|------|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
||x0 |x1 |x2 |x3 |x4 |x5 |x6 |x7 |x8 |x9 |xA |xB |xC |xD |xE |xF |
|0x    |   |   |   |   |   |   |   |   |   |   |LF |   |FF |CR |   |   |
|1x    |   |   |   |   |   |   |   |   |   |   |   |ESC|   |   |   |   |
|2x    |SP |!  |“  |#  |$  |%  |&  |‘  |(  |)  |*  |+  |,  |   |.  |/  |
|3x    |0  |1  |2  |3  |4  |5  |6  |7  |8  |9  |:  |;  |<  |=  |>  |?  |
|4x    |@  |A  |B  |C  |D  |E  |F  |G  |H  |I  |J  |K  |L  |M  |N  |O  |
|5x    |P  |Q  |R  |S  |T  |U  |V  |W  |X  |Y  |Z  |[  |\  |]  |^  |_  |
|6x    |`  |a  |b  |c  |d  |e  |f  |g  |h  |i  |j  |k  |l  |m  |n  |o  |
|7x    |p  |q  |r  |s  |t  |u  |v  |w  |x  |y  |z  |{  |&#124; |}  |~  |   |

