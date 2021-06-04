---
title: 'Learning SQL Notes #4.5: Regular Expression'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "The complement note from Learning SQL #4"

date: "2021-06-02T20:00:00Z"

categories:
  - SQL
tags:
  - SQL notes
  - R
featured: yes
projects: []
---

Adapted from https://docs.microsoft.com/en-us/dotnet/standard/base-types/regular-expression-language-quick-reference

<h2 id="character-escapes">Character Escapes</h2>
<p>The backslash character (\) in a regular expression indicates that the character that follows it either is a special character (as shown in the following table), or should be interpreted literally. For more information, see <a href="character-escapes-in-regular-expressions" data-linktype="relative-path">Character Escapes</a>.</p>
<table>
<thead>
<tr>
<th>Escaped character</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>\a</code></td>
<td>Matches a bell character, \u0007.</td>
<td><code>\a</code></td>
<td><code>&quot;\u0007&quot;</code> in <code>&quot;Error!&quot; + '\u0007'</code></td>
</tr>
<tr>
<td><code>\b</code></td>
<td>In a character class, matches a backspace, \u0008.</td>
<td><code>[\b]{3,}</code></td>
<td><code>&quot;\b\b\b\b&quot;</code> in <code>&quot;\b\b\b\b&quot;</code></td>
</tr>
<tr>
<td><code>\t</code></td>
<td>Matches a tab, \u0009.</td>
<td><code>(\w+)\t</code></td>
<td><code>&quot;item1\t&quot;</code>, <code>&quot;item2\t&quot;</code> in <code>&quot;item1\titem2\t&quot;</code></td>
</tr>
<tr>
<td><code>\r</code></td>
<td>Matches a carriage return, \u000D. (<code>\r</code> is not equivalent to the newline character, <code>\n</code>.)</td>
<td><code>\r\n(\w+)</code></td>
<td><code>&quot;\r\nThese&quot;</code> in <code>&quot;\r\nThese are\ntwo lines.&quot;</code></td>
</tr>
<tr>
<td><code>\v</code></td>
<td>Matches a vertical tab, \u000B.</td>
<td><code>[\v]{2,}</code></td>
<td><code>&quot;\v\v\v&quot;</code> in <code>&quot;\v\v\v&quot;</code></td>
</tr>
<tr>
<td><code>\f</code></td>
<td>Matches a form feed, \u000C.</td>
<td><code>[\f]{2,}</code></td>
<td><code>&quot;\f\f\f&quot;</code> in <code>&quot;\f\f\f&quot;</code></td>
</tr>
<tr>
<td><code>\n</code></td>
<td>Matches a new line, \u000A.</td>
<td><code>\r\n(\w+)</code></td>
<td><code>&quot;\r\nThese&quot;</code> in <code>&quot;\r\nThese are\ntwo lines.&quot;</code></td>
</tr>
<tr>
<td><code>\e</code></td>
<td>Matches an escape, \u001B.</td>
<td><code>\e</code></td>
<td><code>&quot;\x001B&quot;</code> in <code>&quot;\x001B&quot;</code></td>
</tr>
<tr>
<td><code>\</code> <em>nnn</em></td>
<td>Uses octal representation to specify a character (<em>nnn</em> consists of two or three digits).</td>
<td><code>\w\040\w</code></td>
<td><code>&quot;a b&quot;</code>, <code>&quot;c d&quot;</code> in <code>&quot;a bc d&quot;</code></td>
</tr>
<tr>
<td><code>\x</code> <em>nn</em></td>
<td>Uses hexadecimal representation to specify a character (<em>nn</em> consists of exactly two digits).</td>
<td><code>\w\x20\w</code></td>
<td><code>&quot;a b&quot;</code>, <code>&quot;c d&quot;</code> in <code>&quot;a bc d&quot;</code></td>
</tr>
<tr>
<td><code>\c</code> <em>X</em><br/><br/> <code>\c</code> <em>x</em></td>
<td>Matches the ASCII control character that is specified by <em>X</em> or <em>x</em>, where <em>X</em> or <em>x</em> is the letter of the control character.</td>
<td><code>\cC</code></td>
<td><code>&quot;\x0003&quot;</code> in <code>&quot;\x0003&quot;</code> (Ctrl-C)</td>
</tr>
<tr>
<td><code>\u</code> <em>nnnn</em></td>
<td>Matches a Unicode character by using hexadecimal representation (exactly four digits, as represented by <em>nnnn</em>).</td>
<td><code>\w\u0020\w</code></td>
<td><code>&quot;a b&quot;</code>, <code>&quot;c d&quot;</code> in <code>&quot;a bc d&quot;</code></td>
</tr>
<tr>
<td><code>\</code></td>
<td>When followed by a character that is not recognized as an escaped character in this and other tables in this topic, matches that character. For example, <code>\*</code> is the same as <code>\x2A</code>, and <code>\.</code> is the same as <code>\x2E</code>. This allows the regular expression engine to disambiguate language elements (such as * or ?) and character literals (represented by <code>\*</code> or <code>\?</code>).</td>
<td><code>\d+[\+-x\*]\d+</code></td>
<td><code>&quot;2+2&quot;</code> and <code>&quot;3*9&quot;</code> in <code>&quot;(2+2) * 3*9&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="character-classes">Character Classes</h2>
<p>A character class matches any one of a set of characters. Character classes include the language elements listed in the following table. For more information, see <a href="character-classes-in-regular-expressions" data-linktype="relative-path">Character Classes</a>.</p>
<table>
<thead>
<tr>
<th>Character class</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>[</code> <em>character_group</em> <code>]</code></td>
<td>Matches any single character in <em>character_group</em>. By default, the match is case-sensitive.</td>
<td><code>[ae]</code></td>
<td><code>&quot;a&quot;</code> in <code>&quot;gray&quot;</code><br/><br/> <code>&quot;a&quot;</code>, <code>&quot;e&quot;</code> in <code>&quot;lane&quot;</code></td>
</tr>
<tr>
<td><code>[^</code> <em>character_group</em> <code>]</code></td>
<td>Negation: Matches any single character that is not in <em>character_group</em>. By default, characters in <em>character_group</em> are case-sensitive.</td>
<td><code>[^aei]</code></td>
<td><code>&quot;r&quot;</code>, <code>&quot;g&quot;</code>, <code>&quot;n&quot;</code> in <code>&quot;reign&quot;</code></td>
</tr>
<tr>
<td><code>[</code> <em>first</em> <code>-</code> <em>last</em> <code>]</code></td>
<td>Character range: Matches any single character in the range from <em>first</em> to <em>last</em>.</td>
<td><code>[A-Z]</code></td>
<td><code>&quot;A&quot;</code>, <code>&quot;B&quot;</code> in <code>&quot;AB123&quot;</code></td>
</tr>
<tr>
<td><code>.</code></td>
<td>Wildcard: Matches any single character except \n.<br/><br/> To match a literal period character (. or <code>\u002E</code>), you must precede it with the escape character (<code>\.</code>).</td>
<td><code>a.e</code></td>
<td><code>&quot;ave&quot;</code> in <code>&quot;nave&quot;</code><br/><br/> <code>&quot;ate&quot;</code> in <code>&quot;water&quot;</code></td>
</tr>
<tr>
<td><code>\p{</code> <em>name</em> <code>}</code></td>
<td>Matches any single character in the Unicode general category or named block specified by <em>name</em>.</td>
<td><code>\p{Lu}</code><br/><br/> <code>\p{IsCyrillic}</code></td>
<td><code>&quot;C&quot;</code>, <code>&quot;L&quot;</code> in <code>&quot;City Lights&quot;</code><br/><br/> <code>&quot;Д&quot;</code>, <code>&quot;Ж&quot;</code> in <code>&quot;ДЖem&quot;</code></td>
</tr>
<tr>
<td><code>\P{</code> <em>name</em> <code>}</code></td>
<td>Matches any single character that is not in the Unicode general category or named block specified by <em>name</em>.</td>
<td><code>\P{Lu}</code><br/><br/> <code>\P{IsCyrillic}</code></td>
<td><code>&quot;i&quot;</code>, <code>&quot;t&quot;</code>, <code>&quot;y&quot;</code> in <code>&quot;City&quot;</code><br/><br/> <code>&quot;e&quot;</code>, <code>&quot;m&quot;</code> in <code>&quot;ДЖem&quot;</code></td>
</tr>
<tr>
<td><code>\w</code></td>
<td>Matches any word character.</td>
<td><code>\w</code></td>
<td><code>&quot;I&quot;</code>, <code>&quot;D&quot;</code>, <code>&quot;A&quot;</code>, <code>&quot;1&quot;</code>, <code>&quot;3&quot;</code> in <code>&quot;ID A1.3&quot;</code></td>
</tr>
<tr>
<td><code>\W</code></td>
<td>Matches any non-word character.</td>
<td><code>\W</code></td>
<td><code>&quot; &quot;</code>, <code>&quot;.&quot;</code> in <code>&quot;ID A1.3&quot;</code></td>
</tr>
<tr>
<td><code>\s</code></td>
<td>Matches any white-space character.</td>
<td><code>\w\s</code></td>
<td><code>&quot;D &quot;</code> in <code>&quot;ID A1.3&quot;</code></td>
</tr>
<tr>
<td><code>\S</code></td>
<td>Matches any non-white-space character.</td>
<td><code>\s\S</code></td>
<td><code>&quot; _&quot;</code> in <code>&quot;int __ctr&quot;</code></td>
</tr>
<tr>
<td><code>\d</code></td>
<td>Matches any decimal digit.</td>
<td><code>\d</code></td>
<td><code>&quot;4&quot;</code> in <code>&quot;4 = IV&quot;</code></td>
</tr>
<tr>
<td><code>\D</code></td>
<td>Matches any character other than a decimal digit.</td>
<td><code>\D</code></td>
<td><code>&quot; &quot;</code>, <code>&quot;=&quot;</code>, <code>&quot; &quot;</code>, <code>&quot;I&quot;</code>, <code>&quot;V&quot;</code> in <code>&quot;4 = IV&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="anchors">Anchors</h2>
<p>Anchors, or atomic zero-width assertions, cause a match to succeed or fail depending on the current position in the string, but they do not cause the engine to advance through the string or consume characters. The metacharacters listed in the following table are anchors. For more information, see <a href="anchors-in-regular-expressions" data-linktype="relative-path">Anchors</a>.</p>
<table>
<thead>
<tr>
<th>Assertion</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>^</code></td>
<td>By default, the match must start at the beginning of the string; in multiline mode, it must start at the beginning of the line.</td>
<td><code>^\d{3}</code></td>
<td><code>&quot;901&quot;</code> in <code>&quot;901-333-&quot;</code></td>
</tr>
<tr>
<td><code>$</code></td>
<td>By default, the match must occur at the end of the string or before <code>\n</code> at the end of the string; in multiline mode, it must occur before the end of the line or before <code>\n</code> at the end of the line.</td>
<td><code>-\d{3}$</code></td>
<td><code>&quot;-333&quot;</code> in <code>&quot;-901-333&quot;</code></td>
</tr>
<tr>
<td><code>\A</code></td>
<td>The match must occur at the start of the string.</td>
<td><code>\A\d{3}</code></td>
<td><code>&quot;901&quot;</code> in <code>&quot;901-333-&quot;</code></td>
</tr>
<tr>
<td><code>\Z</code></td>
<td>The match must occur at the end of the string or before <code>\n</code> at the end of the string.</td>
<td><code>-\d{3}\Z</code></td>
<td><code>&quot;-333&quot;</code> in <code>&quot;-901-333&quot;</code></td>
</tr>
<tr>
<td><code>\z</code></td>
<td>The match must occur at the end of the string.</td>
<td><code>-\d{3}\z</code></td>
<td><code>&quot;-333&quot;</code> in <code>&quot;-901-333&quot;</code></td>
</tr>
<tr>
<td><code>\G</code></td>
<td>The match must occur at the point where the previous match ended.</td>
<td><code>\G\(\d\)</code></td>
<td><code>&quot;(1)&quot;</code>, <code>&quot;(3)&quot;</code>, <code>&quot;(5)&quot;</code> in <code>&quot;(1)(3)(5)[7](9)&quot;</code></td>
</tr>
<tr>
<td><code>\b</code></td>
<td>The match must occur on a boundary between a <code>\w</code> (alphanumeric) and a <code>\W</code> (nonalphanumeric) character.</td>
<td><code>\b\w+\s\w+\b</code></td>
<td><code>&quot;them theme&quot;</code>, <code>&quot;them them&quot;</code> in <code>&quot;them theme them them&quot;</code></td>
</tr>
<tr>
<td><code>\B</code></td>
<td>The match must not occur on a <code>\b</code> boundary.</td>
<td><code>\Bend\w*\b</code></td>
<td><code>&quot;ends&quot;</code>, <code>&quot;ender&quot;</code> in <code>&quot;end sends endure lender&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="grouping-constructs">Grouping Constructs</h2>
<p>Grouping constructs delineate subexpressions of a regular expression and typically capture substrings of an input string. Grouping constructs include the language elements listed in the following table. For more information, see <a href="grouping-constructs-in-regular-expressions" data-linktype="relative-path">Grouping Constructs</a>.</p>
<table>
<thead>
<tr>
<th>Grouping construct</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>(</code> <em>subexpression</em> <code>)</code></td>
<td>Captures the matched subexpression and assigns it a one-based ordinal number.</td>
<td><code>(\w)\1</code></td>
<td><code>&quot;ee&quot;</code> in <code>&quot;deep&quot;</code></td>
</tr>
<tr>
<td><code>(?&lt;</code> <em>name</em> <code>&gt;</code> <em>subexpression</em> <code>)</code><br/> or <br/><code>(?'</code> <em>name</em> <code>'</code> <em>subexpression</em> <code>)</code></td>
<td>Captures the matched subexpression into a named group.</td>
<td><code>(?&lt;double&gt;\w)\k&lt;double&gt;</code></td>
<td><code>&quot;ee&quot;</code> in <code>&quot;deep&quot;</code></td>
</tr>
<tr>
<td><code>(?&lt;</code> <em>name1</em> <code>-</code> <em>name2</em> <code>&gt;</code> <em>subexpression</em> <code>)</code> <br/> or <br/> <code>(?'</code> <em>name1</em> <code>-</code> <em>name2</em> <code>'</code> <em>subexpression</em> <code>)</code></td>
<td>Defines a balancing group definition. For more information, see the &quot;Balancing Group Definition&quot; section in <a href="grouping-constructs-in-regular-expressions" data-linktype="relative-path">Grouping Constructs</a>.</td>
<td><code>(((?'Open'\()[^\(\)]*)+((?'Close-Open'\))[^\(\)]*)+)*(?(Open)(?!))$</code></td>
<td><code>&quot;((1-3)*(3-1))&quot;</code> in <code>&quot;3+2^((1-3)*(3-1))&quot;</code></td>
</tr>
<tr>
<td><code>(?:</code> <em>subexpression</em> <code>)</code></td>
<td>Defines a noncapturing group.</td>
<td><code>Write(?:Line)?</code></td>
<td><code>&quot;WriteLine&quot;</code> in <code>&quot;Console.WriteLine()&quot;</code><br/><br/> <code>&quot;Write&quot;</code> in <code>&quot;Console.Write(value)&quot;</code></td>
</tr>
<tr>
<td><code>(?imnsx-imnsx:</code> <em>subexpression</em> <code>)</code></td>
<td>Applies or disables the specified options within <em>subexpression</em>. For more information, see <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</td>
<td><code>A\d{2}(?i:\w+)\b</code></td>
<td><code>&quot;A12xl&quot;</code>, <code>&quot;A12XL&quot;</code> in <code>&quot;A12xl A12XL a12xl&quot;</code></td>
</tr>
<tr>
<td><code>(?=</code> <em>subexpression</em> <code>)</code></td>
<td>Zero-width positive lookahead assertion.</td>
<td><code>\b\w+\b(?=.+and.+)</code></td>
<td><code>&quot;cats&quot;</code>, <code>&quot;dogs&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code></td>
</tr>
<tr>
<td><code>(?!</code> <em>subexpression</em> <code>)</code></td>
<td>Zero-width negative lookahead assertion.</td>
<td><code>\b\w+\b(?!.+and.+)</code></td>
<td><code>&quot;and&quot;</code>, <code>&quot;some&quot;</code>, <code>&quot;mice&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code></td>
</tr>
<tr>
<td><code>(?&lt;=</code> <em>subexpression</em> <code>)</code></td>
<td>Zero-width positive lookbehind assertion.</td>
<td><code>\b\w+\b(?&lt;=.+and.+)</code><br/><br/>———————————<br/><br/><code>\b\w+\b(?&lt;=.+and.*)</code></td>
<td><code>&quot;some&quot;</code>, <code>&quot;mice&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code><br/>————————————<br/><code>&quot;and&quot;</code>, <code>&quot;some&quot;</code>, <code>&quot;mice&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code></td>
</tr>
<tr>
<td><code>(?&lt;!</code> <em>subexpression</em> <code>)</code></td>
<td>Zero-width negative lookbehind assertion.</td>
<td><code>\b\w+\b(?&lt;!.+and.+)</code><br/><br/>———————————<br/><br/><code>\b\w+\b(?&lt;!.+and.*)</code></td>
<td><code>&quot;cats&quot;</code>, <code>&quot;dogs&quot;</code>, <code>&quot;and&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code><br/>————————————<br/><code>&quot;cats&quot;</code>, <code>&quot;dogs&quot;</code><br/>in<br/><code>&quot;cats, dogs and some mice.&quot;</code></td>
</tr>
<tr>
<td><code>(?&gt;</code> <em>subexpression</em> <code>)</code></td>
<td>Atomic group.</td>
<td><code>(?&gt;a|ab)c</code></td>
<td><code>&quot;ac&quot;</code> in<code>&quot;ac&quot;</code><br/><br/><em>nothing</em> in<code>&quot;abc&quot;</code></td>
</tr>
</tbody>
</table>
<h3 id="lookarounds-at-a-glance">Lookarounds at a glance</h3>
<p>When the regular expression engine hits a <strong>lookaround expression</strong>, it takes a substring reaching from the current position to the start (lookbehind) or end (lookahead) of the original string, and then runs
<a href="/en-us/dotnet/api/system.text.regularexpressions.regex.ismatch" data-linktype="absolute-path">Regex.IsMatch</a> on that substring using the lookaround pattern. Success of this subexpression's result is then determined by whether it's a positive or negative assertion.</p>
<table>
<thead>
<tr>
<th>Lookaround</th>
<th>Name</th>
<th>Function</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>(?=check)</code></td>
<td>Positive Lookahead</td>
<td>Asserts that what immediately follows the current position in the string is &quot;check&quot;</td>
</tr>
<tr>
<td><code>(?&lt;=check)</code></td>
<td>Positive Lookbehind</td>
<td>Asserts that what immediately precedes the current position in the string is &quot;check&quot;</td>
</tr>
<tr>
<td><code>(?!check)</code></td>
<td>Negative Lookahead</td>
<td>Asserts that what immediately follows the current position in the string is not &quot;check&quot;</td>
</tr>
<tr>
<td><code>(?&lt;!check)</code></td>
<td>Negative Lookbehind</td>
<td>Asserts that what immediately precedes the current position in the string is not &quot;check&quot;</td>
</tr>
</tbody>
</table>
<p>Once they have matched, <strong>atomic groups</strong> won't be re-evaluated again, even when the remainder of the pattern fails due to the match. This can significantly improve performance when quantifiers occur within the atomic group or the remainder of the pattern.</p>
<h2 id="quantifiers">Quantifiers</h2>
<p>A quantifier specifies how many instances of the previous element (which can be a character, a group, or a character class) must be present in the input string for a match to occur. Quantifiers include the language elements listed in the following table. For more information, see <a href="quantifiers-in-regular-expressions" data-linktype="relative-path">Quantifiers</a>.</p>
<table>
<thead>
<tr>
<th>Quantifier</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>*</code></td>
<td>Matches the previous element zero or more times.</td>
<td><code>\d*\.\d</code></td>
<td><code>&quot;.0&quot;</code>, <code>&quot;19.9&quot;</code>, <code>&quot;219.9&quot;</code></td>
</tr>
<tr>
<td><code>+</code></td>
<td>Matches the previous element one or more times.</td>
<td><code>&quot;be+&quot;</code></td>
<td><code>&quot;bee&quot;</code> in <code>&quot;been&quot;</code>, <code>&quot;be&quot;</code> in <code>&quot;bent&quot;</code></td>
</tr>
<tr>
<td><code>?</code></td>
<td>Matches the previous element zero or one time.</td>
<td><code>&quot;rai?n&quot;</code></td>
<td><code>&quot;ran&quot;</code>, <code>&quot;rain&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>}</code></td>
<td>Matches the previous element exactly <em>n</em> times.</td>
<td><code>&quot;,\d{3}&quot;</code></td>
<td><code>&quot;,043&quot;</code> in <code>&quot;1,043.6&quot;</code>, <code>&quot;,876&quot;</code>, <code>&quot;,543&quot;</code>, and <code>&quot;,210&quot;</code> in <code>&quot;9,876,543,210&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>,}</code></td>
<td>Matches the previous element at least <em>n</em> times.</td>
<td><code>&quot;\d{2,}&quot;</code></td>
<td><code>&quot;166&quot;</code>, <code>&quot;29&quot;</code>, <code>&quot;1930&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>,</code> <em>m</em> <code>}</code></td>
<td>Matches the previous element at least <em>n</em> times, but no more than <em>m</em> times.</td>
<td><code>&quot;\d{3,5}&quot;</code></td>
<td><code>&quot;166&quot;</code>, <code>&quot;17668&quot;</code><br/><br/> <code>&quot;19302&quot;</code> in <code>&quot;193024&quot;</code></td>
</tr>
<tr>
<td><code>*?</code></td>
<td>Matches the previous element zero or more times, but as few times as possible.</td>
<td><code>\d*?\.\d</code></td>
<td><code>&quot;.0&quot;</code>, <code>&quot;19.9&quot;</code>, <code>&quot;219.9&quot;</code></td>
</tr>
<tr>
<td><code>+?</code></td>
<td>Matches the previous element one or more times, but as few times as possible.</td>
<td><code>&quot;be+?&quot;</code></td>
<td><code>&quot;be&quot;</code> in <code>&quot;been&quot;</code>, <code>&quot;be&quot;</code> in <code>&quot;bent&quot;</code></td>
</tr>
<tr>
<td><code>??</code></td>
<td>Matches the previous element zero or one time, but as few times as possible.</td>
<td><code>&quot;rai??n&quot;</code></td>
<td><code>&quot;ran&quot;</code>, <code>&quot;rain&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>}?</code></td>
<td>Matches the preceding element exactly <em>n</em> times.</td>
<td><code>&quot;,\d{3}?&quot;</code></td>
<td><code>&quot;,043&quot;</code> in <code>&quot;1,043.6&quot;</code>, <code>&quot;,876&quot;</code>, <code>&quot;,543&quot;</code>, and <code>&quot;,210&quot;</code> in <code>&quot;9,876,543,210&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>,}?</code></td>
<td>Matches the previous element at least <em>n</em> times, but as few times as possible.</td>
<td><code>&quot;\d{2,}?&quot;</code></td>
<td><code>&quot;166&quot;</code>, <code>&quot;29&quot;</code>, <code>&quot;1930&quot;</code></td>
</tr>
<tr>
<td><code>{</code> <em>n</em> <code>,</code> <em>m</em> <code>}?</code></td>
<td>Matches the previous element between <em>n</em> and <em>m</em> times, but as few times as possible.</td>
<td><code>&quot;\d{3,5}?&quot;</code></td>
<td><code>&quot;166&quot;</code>, <code>&quot;17668&quot;</code><br/><br/> <code>&quot;193&quot;</code>, <code>&quot;024&quot;</code> in <code>&quot;193024&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="backreference-constructs">Backreference Constructs</h2>
<p>A backreference allows a previously matched subexpression to be identified subsequently in the same regular expression. The following table lists the backreference constructs supported by regular expressions in .NET. For more information, see <a href="backreference-constructs-in-regular-expressions" data-linktype="relative-path">Backreference Constructs</a>.</p>
<table>
<thead>
<tr>
<th>Backreference construct</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>\</code> <em>number</em></td>
<td>Backreference. Matches the value of a numbered subexpression.</td>
<td><code>(\w)\1</code></td>
<td><code>&quot;ee&quot;</code> in <code>&quot;seek&quot;</code></td>
</tr>
<tr>
<td><code>\k&lt;</code> <em>name</em> <code>&gt;</code></td>
<td>Named backreference. Matches the value of a named expression.</td>
<td><code>(?&lt;char&gt;\w)\k&lt;char&gt;</code></td>
<td><code>&quot;ee&quot;</code> in <code>&quot;seek&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="alternation-constructs">Alternation Constructs</h2>
<p>Alternation constructs modify a regular expression to enable either/or matching. These constructs include the language elements listed in the following table. For more information, see <a href="alternation-constructs-in-regular-expressions" data-linktype="relative-path">Alternation Constructs</a>.</p>
<table>
<thead>
<tr>
<th>Alternation construct</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>|</code></td>
<td>Matches any one element separated by the vertical bar (<code>|</code>) character.</td>
<td><code>th(e|is|at)</code></td>
<td><code>&quot;the&quot;</code>, <code>&quot;this&quot;</code> in <code>&quot;this is the day.&quot;</code></td>
</tr>
<tr>
<td><code>(?(</code> <em>expression</em> <code>)</code> <em>yes</em> <code>|</code> <em>no</em> <code>)</code></td>
<td>Matches <em>yes</em> if the regular expression pattern designated by <em>expression</em> matches; otherwise, matches the optional <em>no</em> part. <em>expression</em> is interpreted as a zero-width assertion.</td>
<td><code>(?(A)A\d{2}\b|\b\d{3}\b)</code></td>
<td><code>&quot;A10&quot;</code>, <code>&quot;910&quot;</code> in <code>&quot;A10 C103 910&quot;</code></td>
</tr>
<tr>
<td><code>(?(</code> <em>name</em> <code>)</code> <em>yes</em> <code>|</code> <em>no</em> <code>)</code></td>
<td>Matches <em>yes</em> if <em>name</em>, a named or numbered capturing group, has a match; otherwise, matches the optional <em>no</em>.</td>
<td><code>(?&lt;quoted&gt;&quot;)?(?(quoted).+?&quot;|\S+\s)</code></td>
<td><code>&quot;Dogs.jpg &quot;</code>, <code>&quot;\&quot;Yiska playing.jpg\&quot;&quot;</code> in <code>&quot;Dogs.jpg \&quot;Yiska playing.jpg\&quot;&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="substitutions">Substitutions</h2>
<p>Substitutions are regular expression language elements that are supported in replacement patterns. For more information, see <a href="substitutions-in-regular-expressions" data-linktype="relative-path">Substitutions</a>. The metacharacters listed in the following table are atomic zero-width assertions.</p>
<table>
<thead>
<tr>
<th>Character</th>
<th>Description</th>
<th>Pattern</th>
<th>Replacement pattern</th>
<th>Input string</th>
<th>Result string</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>$</code> <em>number</em></td>
<td>Substitutes the substring matched by group <em>number</em>.</td>
<td><code>\b(\w+)(\s)(\w+)\b</code></td>
<td><code>$3$2$1</code></td>
<td><code>&quot;one two&quot;</code></td>
<td><code>&quot;two one&quot;</code></td>
</tr>
<tr>
<td><code>${</code> <em>name</em> <code>}</code></td>
<td>Substitutes the substring matched by the named group <em>name</em>.</td>
<td><code>\b(?&lt;word1&gt;\w+)(\s)(?&lt;word2&gt;\w+)\b</code></td>
<td><code>${word2} ${word1}</code></td>
<td><code>&quot;one two&quot;</code></td>
<td><code>&quot;two one&quot;</code></td>
</tr>
<tr>
<td><code>$$</code></td>
<td>Substitutes a literal &quot;$&quot;.</td>
<td><code>\b(\d+)\s?USD</code></td>
<td><code>$$$1</code></td>
<td><code>&quot;103 USD&quot;</code></td>
<td><code>&quot;$103&quot;</code></td>
</tr>
<tr>
<td><code>$&amp;</code></td>
<td>Substitutes a copy of the whole match.</td>
<td><code>\$?\d*\.?\d+</code></td>
<td><code>**$&amp;**</code></td>
<td><code>&quot;$1.30&quot;</code></td>
<td><code>&quot;**$1.30**&quot;</code></td>
</tr>
<tr>
<td><code>$` </code></td>
<td>Substitutes all the text of the input string before the match.</td>
<td><code>B+</code></td>
<td><code>$` </code></td>
<td><code>&quot;AABBCC&quot;</code></td>
<td><code>&quot;AAAACC&quot;</code></td>
</tr>
<tr>
<td><code>$'</code></td>
<td>Substitutes all the text of the input string after the match.</td>
<td><code>B+</code></td>
<td><code>$'</code></td>
<td><code>&quot;AABBCC&quot;</code></td>
<td><code>&quot;AACCCC&quot;</code></td>
</tr>
<tr>
<td><code>$+</code></td>
<td>Substitutes the last group that was captured.</td>
<td><code>B+(C+)</code></td>
<td><code>$+</code></td>
<td><code>&quot;AABBCCDD&quot;</code></td>
<td><code>&quot;AACCDD&quot;</code></td>
</tr>
<tr>
<td><code>$_</code></td>
<td>Substitutes the entire input string.</td>
<td><code>B+</code></td>
<td><code>$_</code></td>
<td><code>&quot;AABBCC&quot;</code></td>
<td><code>&quot;AAAABBCCCC&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="regular-expression-options">Regular Expression Options</h2>
<p>You can specify options that control how the regular expression engine interprets a regular expression pattern. Many of these options can be specified either inline (in the regular expression pattern) or as one or more <a href="/en-us/dotnet/api/system.text.regularexpressions.regexoptions" data-linktype="absolute-path">RegexOptions</a> constants. This quick reference lists only inline options. For more information about inline and <a href="/en-us/dotnet/api/system.text.regularexpressions.regexoptions" data-linktype="absolute-path">RegexOptions</a> options, see the article <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</p>
<p>You can specify an inline option in two ways:</p>
<ul>
<li>By using the <a href="miscellaneous-constructs-in-regular-expressions" data-linktype="relative-path">miscellaneous construct</a> <code>(?imnsx-imnsx)</code>, where a minus sign (-) before an option or set of options turns those options off. For example, <code>(?i-mn)</code> turns case-insensitive matching (<code>i</code>) on, turns multiline mode (<code>m</code>) off, and turns unnamed group captures (<code>n</code>) off. The option applies to the regular expression pattern from the point at which the option is defined, and is effective either to the end of the pattern or to the point where another construct reverses the option.</li>
<li>By using the <a href="grouping-constructs-in-regular-expressions" data-linktype="relative-path">grouping construct</a><code>(?imnsx-imnsx:</code><em>subexpression</em><code>)</code>, which defines options for the specified group only.</li>
</ul>
<p>The .NET regular expression engine supports the following inline options:</p>
<table>
<thead>
<tr>
<th>Option</th>
<th>Description</th>
<th>Pattern</th>
<th>Matches</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>i</code></td>
<td>Use case-insensitive matching.</td>
<td><code>\b(?i)a(?-i)a\w+\b</code></td>
<td><code>&quot;aardvark&quot;</code>, <code>&quot;aaaAuto&quot;</code> in <code>&quot;aardvark AAAuto aaaAuto Adam breakfast&quot;</code></td>
</tr>
<tr>
<td><code>m</code></td>
<td>Use multiline mode. <code>^</code> and <code>$</code> match the beginning and end of a line, instead of the beginning and end of a string.</td>
<td>For an example, see the &quot;Multiline Mode&quot; section in <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</td>
<td></td>
</tr>
<tr>
<td><code>n</code></td>
<td>Do not capture unnamed groups.</td>
<td>For an example, see the &quot;Explicit Captures Only&quot; section in <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</td>
<td></td>
</tr>
<tr>
<td><code>s</code></td>
<td>Use single-line mode.</td>
<td>For an example, see the &quot;Single-line Mode&quot; section in <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</td>
<td></td>
</tr>
<tr>
<td><code>x</code></td>
<td>Ignore unescaped white space in the regular expression pattern.</td>
<td><code>\b(?x) \d+ \s \w+</code></td>
<td><code>&quot;1 aardvark&quot;</code>, <code>&quot;2 cats&quot;</code> in <code>&quot;1 aardvark 2 cats IV centurions&quot;</code></td>
</tr>
</tbody>
</table>
<h2 id="miscellaneous-constructs">Miscellaneous Constructs</h2>
<p>Miscellaneous constructs either modify a regular expression pattern or provide information about it. The following table lists the miscellaneous constructs supported by .NET. For more information, see <a href="miscellaneous-constructs-in-regular-expressions" data-linktype="relative-path">Miscellaneous Constructs</a>.</p>
<table>
<thead>
<tr>
<th>Construct</th>
<th>Definition</th>
<th>Example</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>(?imnsx-imnsx)</code></td>
<td>Sets or disables options such as case insensitivity in the middle of a pattern.For more information, see <a href="regular-expression-options" data-linktype="relative-path">Regular Expression Options</a>.</td>
<td><code>\bA(?i)b\w+\b</code> matches <code>&quot;ABA&quot;</code>, <code>&quot;Able&quot;</code> in <code>&quot;ABA Able Act&quot;</code></td>
</tr>
<tr>
<td><code>(?#</code> <em>comment</em> <code>)</code></td>
<td>Inline comment. The comment ends at the first closing parenthesis.</td>
<td><code>\bA(?#Matches words starting with A)\w+\b</code></td>
</tr>
<tr>
<td><code>#</code> [to end of line]</td>
<td>X-mode comment. The comment starts at an unescaped <code>#</code> and continues to the end of the line.</td>
<td><code>(?x)\bA\w+\b#Matches words starting with A</code></td>
</tr>
</tbody>
</table>
<h2 id="see-also">See also</h2>

<ul>
<li><a href="https://download.microsoft.com/download/D/2/4/D240EBF6-A9BA-4E4F-A63F-AEB6DA0B921C/Regular%20expressions%20quick%20reference.docx" data-linktype="external">Regular Expressions - Quick Reference (download in Word format)</a></li>
<li><a href="https://download.microsoft.com/download/D/2/4/D240EBF6-A9BA-4E4F-A63F-AEB6DA0B921C/Regular%20expressions%20quick%20reference.pdf" data-linktype="external">Regular Expressions - Quick Reference (download in PDF format)</a></li>
</ul>