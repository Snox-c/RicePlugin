package apple.Apples.rice;

import org.bukkit.Bukkit;
import org.bukkit.Material;
import org.bukkit.NamespacedKey;
import org.bukkit.command.Command;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.*;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.entity.EntityDeathEvent;
import org.bukkit.inventory.ItemStack;
import org.bukkit.inventory.meta.ItemMeta;
import org.bukkit.persistence.PersistentDataType;
import org.bukkit.plugin.java.JavaPlugin;

import java.util.Random;

public class RicePlugin extends JavaPlugin implements Listener {

    public static NamespacedKey riceKey;
    private final Random random = new Random();

    @Override 
    public void onEnable() {
        riceKey = new NamespacedKey(this, "rice");
        Bukkit.getPluginManager().registerEvents(this, this);
        getLogger().info("Rice plugin enabled!");
    }

    //this is the command to get rice
    @Override
    public boolean onCommand(CommandSender sender, Command cmd, String label, String[] args) {
        if (!(sender instanceof Player player)) {
            sender.sendMessage("Only players can use this!");
            return true;
        }

        player.getInventory().addItem(getRiceItem());
        player.sendMessage("You received some §aRice §f🌾!");
        return true;
    }

    //the creation of rice
    public static ItemStack getRiceItem() {
        ItemStack item = new ItemStack(Material.WHEAT);
        ItemMeta meta = item.getItemMeta();
        meta.setDisplayName("§fRice §a🌾");
        meta.getPersistentDataContainer().set(riceKey, PersistentDataType.BYTE, (byte) 1);
        item.setItemMeta(meta);
        return item;
    }

    //mob drop rate, 75% from random mobs
    @EventHandler
    public void onEntityDeath(EntityDeathEvent event) {
        LivingEntity entity = event.getEntity();

        if (!(entity instanceof Monster || isBossMob(entity))) return;

        boolean isBoss = isBossMob(entity);

        if (isBoss || random.nextDouble() < 0.75) {
            event.getDrops().add(getRiceItem());
        }
    }

    //boss mobs are 100%
    private boolean isBossMob(Entity entity) {
        return entity instanceof EnderDragon ||
               entity instanceof Wither ||
               entity instanceof Warden;
    }
}
