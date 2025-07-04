import { useState, useEffect } from "react";
import { Heart } from "lucide-react";
import { WishlistItem } from "@/components/WishlistItem";
import { AddItemForm } from "@/components/AddItemForm";

interface WishlistItemType {
  id: string;
  name: string;
  url: string;
  imageUrl?: string;
  dateAdded: string;
}

const Index = () => {
  const [items, setItems] = useState<WishlistItemType[]>([]);

  // Load items from localStorage on component mount
  useEffect(() => {
    const savedItems = localStorage.getItem('yasmin-wishlist');
    if (savedItems) {
      try {
        setItems(JSON.parse(savedItems));
      } catch (error) {
        console.error('Failed to load wishlist items:', error);
      }
    }
  }, []);

  // Save items to localStorage whenever items change
  useEffect(() => {
    localStorage.setItem('yasmin-wishlist', JSON.stringify(items));
  }, [items]);

  const addItem = (name: string, url: string, imageUrl?: string) => {
    const newItem: WishlistItemType = {
      id: crypto.randomUUID(),
      name,
      url,
      imageUrl,
      dateAdded: new Date().toISOString(),
    };
    setItems(prev => [newItem, ...prev]);
  };

  const removeItem = (id: string) => {
    setItems(prev => prev.filter(item => item.id !== id));
  };

  return (
    <div className="min-h-screen bg-gradient-hero">
      <div className="container mx-auto px-4 py-8">
        {/* Header */}
        <div className="text-center mb-12">
          <div className="flex items-center justify-center gap-3 mb-4">
            <Heart className="h-8 w-8 text-primary fill-primary" />
            <h1 className="text-4xl md:text-5xl font-bold bg-gradient-primary bg-clip-text text-transparent">
              yasmin's wishlist
            </h1>
            <Heart className="h-8 w-8 text-primary fill-primary" />
          </div>
          <p className="text-lg text-muted-foreground max-w-2xl mx-auto">
            Your personal collection of items you love and want to remember
          </p>
        </div>

        {/* Stats */}
        {items.length > 0 && (
          <div className="text-center mb-8">
            <div className="inline-block bg-card rounded-full px-6 py-2 shadow-card">
              <span className="text-sm font-medium text-muted-foreground">
                {items.length} {items.length === 1 ? 'item' : 'items'} in your wishlist
              </span>
            </div>
          </div>
        )}

        {/* Items Grid */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
          {/* Add Item Form */}
          <AddItemForm onAdd={addItem} />
          
          {/* Wishlist Items */}
          {items.map((item) => (
            <WishlistItem
              key={item.id}
              id={item.id}
              name={item.name}
              url={item.url}
              imageUrl={item.imageUrl}
              onRemove={removeItem}
            />
          ))}
        </div>

        {/* Empty State */}
        {items.length === 0 && (
          <div className="text-center mt-16">
            <div className="max-w-md mx-auto">
              <Heart className="h-16 w-16 text-muted-foreground mx-auto mb-4" />
              <h3 className="text-xl font-semibold mb-2">Your wishlist is empty</h3>
              <p className="text-muted-foreground">
                Start adding items you love and want to remember. Click the "Add new item" card above to get started!
              </p>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default Index;
