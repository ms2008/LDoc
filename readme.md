# This is a LDoc Fork

Copyright (C) 2011-2012 Steve Donovan.
[LDOC](https://github.com/stevedonovan/LDoc)

## Support Chinese Reference

```lua
-- .\ldoc\html.lua
function ldoc.no_spaces(s)
   s = s:gsub('%s*$','')
   -- return (s:gsub('%W','_'))
   return (s:gsub('%s','_'))
end

-- .\ldoc\markup.lua
function markup.add_sections(F, txt)
   local sections, L, first = {}, 1, true
   local title_pat
   local lstrip = stringx.lstrip
   for line in stringx.lines(txt) do
      if first then
         local level,header = line:match '^(#+)%s*(.+)'
         if level then
            level = level .. '#'
         else
            level = '##'
         end
         title_pat = '^'..level..'([^#]%s*.+)'
         title_pat = lstrip(title_pat)
         first = false
         F.display_name = header
      end
      local title = line:match (title_pat)
      if title then
         -- Markdown allows trailing '#'...
         title = title:gsub('%s*#+$','')
         print(title)
         -- sections[L] = F:add_document_section(lstrip(title))
         sections[L] = F:add_document_section(lstrip(title))
         print(sections[L])
      end
      L = L + 1
   end
   F.sections = sections
   return txt
end

-- .\ldoc\doc.lua
function File:add_document_section(title)
   -- local section = title:gsub('%W','_')
   local section = title:gsub('%s*','')
   self:new_item {
      name = section,
      class = 'section',
      summary = title
   }
   self:new_item {
      name = 'dumbo',
      class = 'function',
   }
   return section
end

-- .\ldoc\tools.lua
function M.extract_identifier (value)
   -- print(value:match('([%.:%-_%S]+)(.*)$'))
   return value:match('([%.:%-_%S]+)(.*)$')
   -- return value:match('([%.:%-_%w]+)(.*)$')
end
```


