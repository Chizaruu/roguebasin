# Ruby precise permissive FOV implementation

---

This is an implementation of [Precise Permissive Field of View](precise_permissive_field_of_view.md)) algorithm. It is based on the [Python implementation](permissive_field_of_view_in_python.md) by Aaron MacDonald.

To use the algorithm, create a Map class or somesuch and provide two methods:

blocked?(x, y) returns true if the tile at (x, y) blocks view of tiles beyond it (e.g. walls)
light(x, y) marks (x, y) as visible to the player (e.g. lit up on screen)

Then include PermissiveFieldOfView within your Map class (or call extend(PermissiveFieldOfView) on your Map instance if you want to be dynamic) and call do_fov.

Compared with [shadowcasting](ruby_shadowcasting_implementation.md), permissive FOV runs ever so slightly slower but is reputedly artifact-free and symmetric (if you can see something, it can see you). Unfortunately this algorithm produces a square FOV shape around the player. Uncommenting the lines in visit_coord will change it to a diamond, but I don't understand enough to get a circle out of it :(

My Ruby is fairly novice so if anyone sees anything amiss with the code, you are more than welcome to change it.

```ruby
module PermissiveFieldOfView
    # Determines which co-ordinates on a 2D grid are visible
    # from a particular co-ordinate.
    # start_x, start_y: center of view
    # radius: how far field of view extends
    def do_fov(start_x, start_y, radius)
        @start_x, @start_y = start_x, start_y
        @radius_sq = radius * radius

        # We always see the center
        light @start_x, @start_y

        # Restrict scan dimensions to map borders and within the radius
        min_extent_x = [@start_x, radius].min
        max_extent_x = [@width - @start_x - 1, radius].min
        min_extent_y = [@start_y, radius].min
        max_extent_y = [@height - @start_y - 1, radius].min

        # Check quadrants: NE, SE, SW, NW
        check_quadrant  1,  1, max_extent_x, max_extent_y
        check_quadrant  1, -1, max_extent_x, min_extent_y
        check_quadrant -1, -1, min_extent_x, min_extent_y
        check_quadrant -1,  1, min_extent_x, max_extent_y
    end

    private
    # Represents a line (duh)
    class Line < Struct.new(:xi, :yi, :xf, :yf)
        # Macros to make slope comparisons clearer
        {:below => '>', :below_or_collinear => '>=', :above => '<',
            :above_or_collinear => '<=', :collinear => '=='}.each do |name, fn|
            eval "def #{name.to_s}?(x, y) relative_slope(x, y) #{fn} 0 end"
        end

        def dx; xf - xi end
        def dy; yf - yi end

        def line_collinear?(line)
            collinear?(line.xi, line.yi) and collinear?(line.xf, line.yf)
        end

        def relative_slope(x, y)
            (dy * (xf - x)) - (dx * (yf - y))
        end
    end

    class ViewBump < Struct.new(:x, :y, :parent)
        def deep_copy
            ViewBump.new(x, y, parent.nil? ? nil : parent.deep_copy)
        end
    end

    class View < Struct.new(:shallow_line, :steep_line)
        attr_accessor :shallow_bump, :steep_bump
        def deep_copy
            copy = View.new(shallow_line.dup, steep_line.dup)
            copy.shallow_bump = shallow_bump.nil? ? nil : shallow_bump.deep_copy
            copy.steep_bump   = steep_bump.nil? ? nil : steep_bump.deep_copy
            return copy
        end
    end

    # Check a quadrant of the FOV field for visible tiles
    def check_quadrant(dx, dy, extent_x, extent_y)
        active_views = []
        shallow_line = Line.new(0, 1, extent_x, 0)
        steep_line = Line.new(1, 0, 0, extent_y)

        active_views << View.new(shallow_line, steep_line)
        view_index = 0

        # Visit the tiles diagonally and going outwards
        i, max_i = 1, extent_x + extent_y
        while i != max_i + 1 and active_views.size > 0
            start_j = [0, i - extent_x].max
            max_j = [i, extent_y].min
            j = start_j
            while j != max_j + 1 and view_index < active_views.size
                x, y = i - j, j
                visit_coord x, y, dx, dy, view_index, active_views
                j += 1
            end
            i += 1
        end
    end

    def visit_coord(x, y, dx, dy, view_index, active_views)
        # The top left and bottom right corners of the current coordinate
        top_left, bottom_right = [x, y + 1], [x + 1, y]

        while view_index < active_views.size and
            active_views[view_index].steep_line.below_or_collinear?(*bottom_right)
            # Co-ord is above the current view and can be ignored (steeper fields may need it though)
            view_index += 1
        end

        if view_index == active_views.size or
            active_views[view_index].shallow_line.above_or_collinear?(*top_left)
            # Either current co-ord is above all the fields, or it is below all the fields
            return
        end

        # Current co-ord must be between the steep and shallow lines of the current view
        # The real quadrant co-ordinates:
        real_x, real_y = x * dx, y * dy
        coord = [@start_x + real_x, @start_y + real_y]
        light *coord

        # Don't go beyond circular radius specified
        #if (real_x * real_x + real_y * real_y) > @radius_sq
        #    active_views.delete_at(view_index)
        #    return
        #end

        # If this co-ord does not block sight, it has no effect on the view
        return unless blocked?(*coord)

        view = active_views[view_index]
        if view.shallow_line.above?(*bottom_right) and view.steep_line.below?(*top_left)
            # Co-ord is intersected by both lines in current view, and is completely blocked
            active_views.delete(view)
        elsif view.shallow_line.above?(*bottom_right)
            # Co-ord is intersected by shallow line; raise the line
            add_shallow_bump top_left[0], top_left[1], view
            check_view active_views, view_index
        elsif view.steep_line.below?(*top_left)
            # Co-ord is intersected by steep line; lower the line
            add_steep_bump bottom_right[0], bottom_right[1], view
            check_view active_views, view_index
        else
            # Co-ord is completely between the two lines of the current view. Split the
            # current view into two views above and below the current co-ord.
            shallow_view_index, steep_view_index = view_index, view_index += 1
            active_views.insert(shallow_view_index, active_views[shallow_view_index].deep_copy)
            add_steep_bump bottom_right[0], bottom_right[1], active_views[shallow_view_index]

            unless check_view(active_views, shallow_view_index)
                view_index -= 1
                steep_view_index -= 1
            end

            add_shallow_bump top_left[0], top_left[1], active_views[steep_view_index]
            check_view active_views, steep_view_index
       end
    end

    def add_shallow_bump(x, y, view)
        view.shallow_line.xf = x
        view.shallow_line.yf = y
        view.shallow_bump = ViewBump.new(x, y, view.shallow_bump)

        cur_bump = view.steep_bump
        while not cur_bump.nil?
            if view.shallow_line.above?(cur_bump.x, cur_bump.y)
                view.shallow_line.xi = cur_bump.x
                view.shallow_line.yi = cur_bump.y
            end
            cur_bump = cur_bump.parent
        end
    end

    def add_steep_bump(x, y, view)
        view.steep_line.xf = x
        view.steep_line.yf = y
        view.steep_bump = ViewBump.new(x, y, view.steep_bump)

        cur_bump = view.shallow_bump
        while not cur_bump.nil?
            if view.steep_line.below?(cur_bump.x, cur_bump.y)
                view.steep_line.xi = cur_bump.x
                view.steep_line.yi = cur_bump.y
            end
            cur_bump = cur_bump.parent
        end
    end

    # Removes the view in active_views at index view_index if:
    # * The two lines are collinear
    # * The lines pass through either extremity
    def check_view(active_views, view_index)
        shallow_line = active_views[view_index].shallow_line
        steep_line = active_views[view_index].steep_line
        if shallow_line.line_collinear?(steep_line) and (shallow_line.collinear?(0, 1) or
            shallow_line.collinear?(1, 0))
            active_views.delete_at view_index
            return false
        end
        return true
    end
end
```
