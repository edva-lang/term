# term

Terminal formatting, ANSI colors, and text styling for the [Edva](https://github.com/edva-lang/edva) programming language.

## Features

- **Text Colors**: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `gray`
- **Background Colors**: `bg_black`, `bg_red`, `bg_green`, `bg_yellow`, etc. (also available via `@term/color`)
- **Text Styles**: `bold`, `dim`, `italic`, `underline`
- **Terminal Utilities**: `strip` (remove escape sequences), `is_terminal` (check if fd is a TTY)

## Usage

```dva
#use io
#use @term

io::out $ term::green("Success!")
io::out $ term::bold(term::red("Error: something failed"))
io::out $ term::bg_blue(term::white(" Blue Banner "))

// Check if stdout is an interactive terminal
term::is_terminal(1)
   | io::out("Interactive TTY")
   | io::out("Redirected output")
```

## License

MIT
