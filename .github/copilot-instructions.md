# Copilot Instructions for super-easy-vim

## Project Overview

Super Easy Vim is a **joke application** designed to give vim beginners success experiences. It's a dramatically simplified vim subset with intentionally **exaggerated UI** showing current mode and available keybindings that occupy most of the screen - enabling vim operations without cheat sheets.

**Core Philosophy**: Extreme simplification + excessive helpfulness = successful vim learning experience

## Architecture Pattern

This codebase uses a **state machine-driven TUI architecture**:

```rust
// Central state management in src/main.rs
enum Mode {
    Normal,
    Insert, 
    Visual,
    Operator(char),  // Pending operations like 'd' waiting for motion
}

enum Transition {
    Mode(Mode),      // Change mode
    Pending(Input),  // Store incomplete command (like 'g' waiting for 'g')
    Quit,           // Exit application
    Nop,            // No operation
}
```

**Key insight**: The `Vim` struct manages both current mode AND pending input for multi-keystroke commands like `gg` or `dd`.

## Technology Stack & Dependencies

- **ratatui 0.28**: Terminal UI framework (handles layouts, widgets)
- **tui-textarea 0.6**: Text editing widget with vim-like operations
- **crossterm 0.28**: Cross-platform terminal control

**Critical**: All vim functionality is built on top of tui-textarea's capabilities. Check its API before implementing new features.

## Development Workflow

### Build & Run
```bash
cargo run                    # Run with empty buffer
cargo run path/to/file.txt   # Run with file loaded
```

**No tests currently exist** - this is intentional for the MVP/WIP state.

## Implementation Patterns

### Mode Transitions
The state machine handles all vim mode logic:
```rust
// In transition() method - each input triggers state evaluation
match self.mode {
    Mode::Normal => {
        match input.key {
            Key::Char('i') => Transition::Mode(Mode::Insert),
            Key::Char('d') => Transition::Mode(Mode::Operator('d')),
            // ...
        }
    }
}
```

### Multi-keystroke Commands
For commands requiring multiple keys (like `gg`, `dd`, `dw`):
1. First key puts mode into `Operator(char)` or stores in `pending`
2. Second key completes the command and executes via textarea methods
3. Returns to `Mode::Normal`

### UI Pattern - Mode Display
Each mode provides its own UI styling:
```rust
impl Mode {
    fn block<'a>(&self) -> Block<'a> {
        let help = match self {
            Self::Normal => "type q to quit, type i to enter insert mode",
            // Extremely verbose help text is INTENTIONAL
        };
    }
    
    fn cursor_style(&self) -> Style {
        // Different colors per mode for visual feedback
    }
}
```

## Planned Future Features (from README analysis)

1. **Command mode**: `:w`, `:q`, `:wq`, `:q!` only - no other ex commands
2. **Balloon UI**: Mode display near cursor (3 implementation approaches documented in README)
3. **Graduation mode**: Progressive hint reduction with "exam" to advance to real vim
4. **Enhanced dd/dw**: Currently basic, may expand operator+motion combinations

## Development Constraints

### What to AVOID
- **Complex vim features**: No search, macros, windows, buffers, registers (beyond basic yank/paste)
- **Number prefixes**: No `3dw` or `5j` - state machine complexity explodes
- **Advanced motions**: Stick to basic `hjkl`, `w`, `e`, `b`, `^`, `$`
- **Japanese IME**: Explicitly out of scope

### What to PRIORITIZE  
- **Beginner-friendly messaging**: Error messages should be extremely helpful
- **Visual feedback**: Exaggerated mode indicators and available command hints
- **State machine clarity**: Keep the transition logic simple and readable

## File Structure Conventions

- **Single main.rs**: Monolithic by design for simplicity
- **No modules**: All code in main.rs until complexity demands splitting
- **Inline documentation**: README contains extensive design discussions

## Key Code Locations

- `Mode` enum + impl: Core state definitions and UI styling
- `Vim` struct: State machine controller
- `transition()` method: All input handling logic
- `main()` loop: Terminal setup + event loop + cleanup

When modifying vim behavior, always update both the state transition logic AND the corresponding help text in `Mode::block()`.