# (yacine's fork of) llm.nvim

A neovim plugin for no frills LLM-assisted programming.

https://github.com/melbaldove/llm.nvim/assets18225174/9bdc2fa1-ade4-48f2-87ce-3019fc323262


Yacine: "yes!! this is personal and mostly unmaintained. Use the main!"

# Yacine's configuration for this specific fork 
```lua
{
    'yacinemtb/llm.nvim',
    dependencies = { 'nvim-neotest/nvim-nio' },
    config = function()
      -- Manually configure the services
      local system_prompt =
        'You should replace the code that you are sent, only following the comments. Do not talk at all. Only output valid code. Do not provide any backticks that surround the code. Never ever output backticks like this ```. Any comment that is asking you for something should be removed after you satisfy them. Other comments should left alone. Do not output backticks'
      local helpful_prompt = 'You are a helpful assistant. What I have sent are my notes so far. You are very curt, yet helpful.'
      require('llm').setup {
        timeout_ms = 3000,
        services = {
          groq = {
            url = 'https://api.groq.com/openai/v1/chat/completions',
            model = 'llama3-70b-8192',
            api_key_name = 'GROQ_API_KEY',
            system_prompt = system_prompt,
          },
          groq_help = {
            url = 'https://api.groq.com/openai/v1/chat/completions',
            model = 'llama3-70b-8192',
            api_key_name = 'GROQ_API_KEY',
            system_prompt = helpful_prompt,
          },
          openai = {
            url = 'https://api.openai.com/v1/chat/completions',
            model = 'gpt-4o',
            api_key_name = 'OPENAI_API_KEY',
            system_prompt = system_prompt,
          },
          openai_help = {
            url = 'https://api.openai.com/v1/chat/completions',
            model = 'gpt-4o',
            api_key_name = 'OPENAI_API_KEY',
            system_prompt = helpful_prompt,
          },
          claude = {
            url = 'https://api.anthropic.com/v1/messages',
            model = 'claude-3-5-sonnet-20240620',
            api_key_name = 'ANTHROPIC_API_KEY',
            system_prompt = system_prompt,
          },
          claude_help = {
            url = 'https://api.anthropic.com/v1/messages',
            model = 'claude-3-5-sonnet-20240620',
            api_key_name = 'ANTHROPIC_API_KEY',
            system_prompt = helpful_prompt,
          },
        },
      }

      vim.keymap.set('v', '<leader>k', function()
        require('llm').prompt { replace = true, service = 'groq' }
      end, { desc = 'Prompt with groq (replace = true)' })

      vim.keymap.set('v', '<leader>K', function()
        require('llm').prompt { replace = false, service = 'groq_help' }
      end, { desc = 'Prompt with groq (replace = false)' })

      vim.keymap.set('v', '<leader>L', function()
        require('llm').prompt { replace = false, service = 'openai_help' }
      end, { desc = 'Prompt with openai (replace = false)' })

      vim.keymap.set('v', '<leader>l', function()
        require('llm').prompt { replace = true, service = 'openai' }
      end, { desc = 'Prompt with openai (replace = true)' })

      vim.keymap.set('n', '<leader>I', function()
        require('llm').prompt { replace = false, service = 'anthropic' }
      end, { desc = 'Prompt with anthropic (replace = false)' })

      vim.keymap.set('n', '<leader>i', function()
        require('llm').prompt { replace = true, service = 'anthropic_help' }
      end, { desc = 'Prompt with anthropic (replace = true)' })
    end,
  }
```


### Installation

Before using the plugin, set any of `GROQ_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY` env vars with your api keys.

lazy.nvim
```lua
{
    "melbaldove/llm.nvim",
    dependencies = { "nvim-neotest/nvim-nio" }
}
```

### Usage

**`setup()`**

Configure the plugin. This can be omitted to use the default configuration.

```lua
require('llm').setup({
    -- How long to wait for the request to start returning data.
    timeout_ms = 10000,
    services = {
        -- Supported services configured by default
        -- groq = {
        --     url = "https://api.groq.com/openai/v1/chat/completions",
        --     model = "llama3-70b-8192",
        --     api_key_name = "GROQ_API_KEY",
        -- },
        -- openai = {
        --     url = "https://api.openai.com/v1/chat/completions",
        --     model = "gpt-4o",
        --     api_key_name = "OPENAI_API_KEY",
        -- },
        -- anthropic = {
        --     url = "https://api.anthropic.com/v1/messages",
        --     model = "claude-3-5-sonnet-20240620",
        --     api_key_name = "ANTHROPIC_API_KEY",
        -- },

        -- Extra OpenAI-compatible services to add (optional)
        other_provider = {
            url = "https://example.com/other-provider/v1/chat/completions",
            model = "llama3",
            api_key_name = "OTHER_PROVIDER_API_KEY",
        }
    }
})
```

**`prompt()`**

Triggers the LLM assistant. You can pass an optional `replace` flag to replace the current selection with the LLM's response. The prompt is either the visually selected text or the file content up to the cursor if no selection is made.

**`create_llm_md()`**

Creates a new `llm.md` file in the current working directory, where you can write questions or prompts for the LLM.

**Example Bindings**
```lua
vim.keymap.set("n", "<leader>m", function() require("llm").create_llm_md() end)

-- keybinds for prompting with groq
vim.keymap.set("n", "<leader>,", function() require("llm").prompt({ replace = false, service = "groq" }) end)
vim.keymap.set("v", "<leader>,", function() require("llm").prompt({ replace = false, service = "groq" }) end)
vim.keymap.set("v", "<leader>.", function() require("llm").prompt({ replace = true, service = "groq" }) end)

-- keybinds for prompting with openai
vim.keymap.set("n", "<leader>g,", function() require("llm").prompt({ replace = false, service = "openai" }) end)
vim.keymap.set("v", "<leader>g,", function() require("llm").prompt({ replace = false, service = "openai" }) end)
vim.keymap.set("v", "<leader>g.", function() require("llm").prompt({ replace = true, service = "openai" }) end)
```

### Roadmap
- [ollama](https://github.com/ollama/ollama) support

### Credits

- Special thanks to [yacine](https://twitter.com/i/broadcasts/1kvJpvRPjNaKE) and his ask.md vscode plugin for inspiration!
