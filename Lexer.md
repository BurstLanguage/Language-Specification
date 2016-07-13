# Burst
## Tokenization and Lexical Analysis

### Tokenization

Tokenization is the first step of a Burst programs lifecycle. 
It takes in input file(s) and produces a list of "Tokens" which are fed into the next step, the Lexical Analyzer.

In order to build the list of tokens, we'll iterate over each character in the provided input file.
Each character found within the file will then be evaluated to determine whether or not it is valid.

If a character is not considered valid, the tokenizer will exit. Otherwise, if a character is deemed valid, we will assign it a single-token-type.

#### Validation of characters

In order for a character to be considered valid, it must be mappable to one or more of the following single-token-types:

Note: in the following table, possible values for each single-token-type are expressed in the form of a regular expression pattern.

* Letter - `/[A-Za-z]/`
* Number - `/[\d]+/`
* String - `/(\"|\'){1}[\s\S]*(\"|\'){1}/`
* Other (`$`, `_`) - `/($|_)/`
* Semicolon and EOL (End Of Line) - `/;/` - I doubt we need this, it may be handled elsewhere or just used to deliminate software execution.
* Equality Operators (`>`, `<`, `==`, `!=`, `<=`, `>=`) - `/(!=|==|>=|<=|>|<)/`
* Logical Operators (`&`, `|`, `!`) - `/(&&|\|\||!)/`
* Mathematical Operators (`+`, `-`, `*`, `/`, `%`) - `/(\+|\-|(?![0-9])\s*\*\s*(?=[0-9])|\/|%)/`
* Bitwise Operators (`&`, `|`, `^`, `~`) - `/(?!([0-9]+\s*))(&|\||\^|~)(?=\s*[0-9]+)/`
* Pointers & References (`*`, `&`) - `/((?=(\s*))\*(?=(\s*[A-Za-z$_]))|&(?=(\s*[A-Za-z$_])))/`
* Blocks (`{`, `}`, `(`, `)`) - `/({|\(){1}[\s\S]*(}|\)){1}/` - I don't think we need this to be interpreted as a token, but I'm still unsure, it may just be too ambiguous.

If a character cannot be mapped to at least one of the previously documented single-token-types, it is considered to be an invalid character.

Note: These are the same characters used in the GCC Tokenizer. This is to keep on par with C and the characters possible through it.

### Sample output

#### Sample program

    int myInteger = 10;

Given our sample program, the first step of the lexer might output something like follows:

    {
        'i' - Character,
        'n' - Character,
        't' - Character,
        ' ' - Whitespace,
        'm' - Character,
        'y' - Character,
        'I' - Character,
        'n' - Character,
        't' - Character,
        'e' - Character,
        'g' - Character,
        'e' - Character,
        'r' - Character,
        ' ' - Whitespace,
        '=' - Operator,
        ' ' - Whitespace,
        '1' - Number,
        '0' - Number,
        ';' - Terminator
    }


### Lexical Analysis

If a character is considered to be valid (see "Validation of characters"), it will then be converted to a single-token.
A single-token, is not much more than a character, with an attribute to describe its single-token-type.
If a single-token for the 'a' character, was to be expressed using the JSON format, it might look somewhat like follows:

```json
{
    "type": "Character",
    "value": 'a'
}
```

Please note the inclusion of whitespace tokens in the above example. These tokens are important as they are used to help the lexer (in step 2) determine where a potential combined-token starts and ends.

### Building combined tokens

A combined-token, consists of one or more single-tokens, and an attribute to describe the combined-token's type.

Generally speaking, in order for a set of single-tokens to be compiled into one combined-token, each of their single-token-types, needs to match.

To build a combined-token, the lexer will iterate through the previously generated list of single-tokens, and check each single-token's type. If the current single-token has a single-token-type which matches that of the previous single-token, the lexer will combine the previous and current single-tokens, into one combined-token.

Combining of single-tokens that have matching types (or a "combining session"), will stop once a whitespace token is encountered.

Once a "combining session" has stopped, the combined-token that was built, will then be evaluated to determine its combined-token-type.

A list of combined-token-types, as well as their possible values, can be found below:

 * Keyword:
    * `int`
    * `void`
 * Operator:
    * `+`
    * `-`
    * `*`
    * `/`
    * `=`