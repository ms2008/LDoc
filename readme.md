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

## Copy ldoc.logo automatically

```lua
-- .\bin\ldoc.lua
ldoc.logo = 'ldoc.png'

-- .\ldoc\html.lua
local logo = ldoc.logo

if css then -- has CSS been copied?
   check_file(args.dir..css, path.join(args.style,css))
   check_file(args.dir..logo, path.join(args.style,logo))
end

if css then
   ldoc.css = '../'..css
   ldoc.logo = '../'..logo
end

-- .\ldoc\tools.lua
function M.check_file (f,original)
   if not path.exists(f) or path.getmtime(original) > path.getmtime(f) then
      local text,err = utils.readfile(original,true)
      if text then
         text,err = utils.writefile(f,text,true)
      end
      if err then
         quit("Could not copy "..original.." to "..f)
      end
   end
end
```

Yet you also need to modify the pl.utils to support operator files in binary mode.

```lua
--- return the contents of a file as a string
-- @param filename The file path
-- @param is_bin open in binary mode
-- @return file contents
function utils.readfile(filename,is_bin)
    local mode = is_bin and 'b' or ''
    utils.assert_string(1,filename)
    local f,err = io.open(filename,'r'..mode)
    if not f then return utils.raise (err) end
    local res,err = f:read('*a')
    f:close()
    if not res then return raise (err) end
    return res
end

--- write a string to a file
-- @param filename The file path
-- @param str The string
-- @return true or nil
-- @return error message
-- @raise error if filename or str aren't strings
function utils.writefile(filename,str,is_bin)
    local mode = is_bin and 'b' or ''
    utils.assert_string(1,filename)
    utils.assert_string(2,str)
    local f,err = io.open(filename,'w'..mode)
    if not f then return raise(err) end
    f:write(str)
    f:close()
    return true
end
```