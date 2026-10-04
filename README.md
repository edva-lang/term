# term

Terminal formatting, ANSI colors, and text styling for the [Edva](https://github.com/edva-lang/edva) programming language.

## Features

- **Text Colors**: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `gray`
- **Background Colors**: `bg_black`, `bg_red`, `bg_green`, `bg_yellow`, etc. (via `color` submodule)
- **Text Styles**: `bold`, `dim`, `italic`, `underline`
- **Terminal Utilities**: `strip` (remove escape sequences), `is_terminal` (check if fd is a TTY)

## Usage

```dva
#use @term
#use @term/color

print $ term::green("Success!")
print $ term::bold(term::red("Error: something failed"))

// Check if stdout is an interactive terminal
term::is_terminal(1)
   | print $ "Interactive TTY"
   | print $ "Redirected output"
```

## License

MIT
