# OptifineM5-source-patched
Want to code a 1.8 client/"jar mod" without Forge ? Here is the Optifine M5 source code free from Forge hooks.

After coding a PvP Client for a while, I realized I needed to patch a lot of things in MC source code every time I created a new workspace, so I worked a bit on a source code that I will reuse.<br>
Patches include : <br>
- Fixing variable names, though not in all classes. ```GameSettings``` was especially tricky because all the keybind names are initially shifted up.<br>
- Using primitives over Object whenever possible. The decompiling process left some beautiful things such as ```Integer.valueOf(int)```, ```Boolean.valueOf(boolean)```, etc. We want to avoid that as much as possible in our code for it not to hinder the compiler.<br>
- Using ```switch()``` over ```if...else if....else``` for readability, though I turned crazy after a few files.<br>
- Removing useless brackets that cause headache to readers in the first 100 files in the ```net.minecraft.block``` package. If you like brackets even for a single instruction, then switch from eclipse to IntelliJ.<br>
- Removing **unused** ```AtomicInteger field_175373_f``` in ```net.minecraft.client.gui.GuiMainMenu```.<br>
- Removing every single Forge hooks in ```net.optifine.reflect.Reflector``` that are being null-checked every time a Forge event would have been posted if we were on Forge.<br>
- Removing every classes except ```ITransformation``` located in the ```net.minecraftforge``` package which is of no use. ```ITransformation``` was thus put into ```net.minecraft.client.renderer.block.model```.<br>
<br>

# Samples
## net.minecraft.block.BlockDoor.java
### before :
```java
private void setBoundBasedOnMeta(int combinedMeta)
    {
        float f = 0.1875F;
        this.setBlockBounds(0.0F, 0.0F, 0.0F, 1.0F, 2.0F, 1.0F);
        EnumFacing enumfacing = getFacing(combinedMeta);
        boolean flag = isOpen(combinedMeta);
        boolean flag1 = isHingeLeft(combinedMeta);

        if (flag)
        {
            if (enumfacing == EnumFacing.EAST)
            {
                if (!flag1)
                {
                    this.setBlockBounds(0.0F, 0.0F, 0.0F, 1.0F, 1.0F, f);
                }
                else
                {
                    this.setBlockBounds(0.0F, 0.0F, 1.0F - f, 1.0F, 1.0F, 1.0F);
                }
            }
            else if (enumfacing == EnumFacing.SOUTH)
            {
                if (!flag1)
                {
                    this.setBlockBounds(1.0F - f, 0.0F, 0.0F, 1.0F, 1.0F, 1.0F);
                }
                else
                {
                    this.setBlockBounds(0.0F, 0.0F, 0.0F, f, 1.0F, 1.0F);
                }
            }
            else if (enumfacing == EnumFacing.WEST)
            {
                if (!flag1)
                {
                    this.setBlockBounds(0.0F, 0.0F, 1.0F - f, 1.0F, 1.0F, 1.0F);
                }
                else
                {
                    this.setBlockBounds(0.0F, 0.0F, 0.0F, 1.0F, 1.0F, f);
                }
            }
            else if (enumfacing == EnumFacing.NORTH)
            {
                if (!flag1)
                {
                    this.setBlockBounds(0.0F, 0.0F, 0.0F, f, 1.0F, 1.0F);
                }
                else
                {
                    this.setBlockBounds(1.0F - f, 0.0F, 0.0F, 1.0F, 1.0F, 1.0F);
                }
            }
        }
        else if (enumfacing == EnumFacing.EAST)
        {
            this.setBlockBounds(0.0F, 0.0F, 0.0F, f, 1.0F, 1.0F);
        }
        else if (enumfacing == EnumFacing.SOUTH)
        {
            this.setBlockBounds(0.0F, 0.0F, 0.0F, 1.0F, 1.0F, f);
        }
        else if (enumfacing == EnumFacing.WEST)
        {
            this.setBlockBounds(1.0F - f, 0.0F, 0.0F, 1.0F, 1.0F, 1.0F);
        }
        else if (enumfacing == EnumFacing.NORTH)
        {
            this.setBlockBounds(0.0F, 0.0F, 1.0F - f, 1.0F, 1.0F, 1.0F);
        }
    }
```
### after :
```java
private void setBoundBasedOnMeta(int combinedMeta)
    {
        this.setBlockBounds(0f, 0f, 0f, 1f, 2f, 1f);
        EnumFacing enumfacing = getFacing(combinedMeta);
        boolean flag = isOpen(combinedMeta);
        boolean flag1 = isHingeLeft(combinedMeta);
        
        switch(enumfacing) {
        case EAST:
        	if (flag) {
        		if (flag1)
        			this.setBlockBounds(0f, 0f, 0.8125f, 1f, 1f, 1f);
        		else
        			this.setBlockBounds(0f, 0f, 0f, 1f, 1f, 0.1875f);
        	}else
        		this.setBlockBounds(0f, 0f, 0f, 0.1875f, 1f, 1f);
        	
        	break;
        case SOUTH:
        	if (flag) {
        		if (flag1)
        			this.setBlockBounds(0f, 0f, 0f, 0.1875f, 1f, 1f);
        		else
        			this.setBlockBounds(0.8125f, 0f, 0f, 1f, 1f, 1f);
        	}else
        		this.setBlockBounds(0f, 0f, 0f, 1f, 1f, 0.1875f);
        	break;
        	
        case WEST:
        	if (flag) {
        		if (flag1)
        			this.setBlockBounds(0f, 0f, 0f, 1f, 1f, 0.1875f);
        		else
        			this.setBlockBounds(0f, 0f, 0.8125f, 1f, 1f, 1f);
        	}else
        		this.setBlockBounds(0.8125f, 0f, 0f, 1f, 1f, 1f);
        	break;
        case NORTH:
        	if (flag) {
        		if (flag1)
        			this.setBlockBounds(0.8125f, 0f, 0f, 1f, 1f, 1f);
        		else
        			this.setBlockBounds(0f, 0f, 0f, 0.1875f, 1f, 1f);
        	}else
        		this.setBlockBounds(0f, 0f, 0.8125f, 1f, 1f, 1f);
        	break;
        default:
        }
        
    }
```

## net.minecraft.block.BlockRedstoneRepeater.java
### before :
```java
protected IBlockState getUnpoweredState(IBlockState poweredState)
    {
        Integer integer = (Integer)poweredState.getValue(DELAY);
        Boolean obool = (Boolean)poweredState.getValue(LOCKED);
        EnumFacing enumfacing = (EnumFacing)poweredState.getValue(FACING);
        return Blocks.unpowered_repeater.getDefaultState().withProperty(FACING, enumfacing).withProperty(DELAY, integer).withProperty(LOCKED, obool);
    }
```
### after :
```java
protected IBlockState getUnpoweredState(IBlockState poweredState)
    {
        return Blocks.unpowered_repeater.getDefaultState()
        		.withProperty(FACING, poweredState.getValue(FACING))
        		.withProperty(DELAY, poweredState.getValue(DELAY))
        		.withProperty(LOCKED, poweredState.getValue(LOCKED));
    }
```

