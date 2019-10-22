# CS-1027-Assignment-02
Learn about ArrayStacks

import java.io.FileNotFoundException;
import java.io.IOException;

/**
 * This class represents the path that Bryan takes in the various maps and the
 * algorithm that Bryan follows to navigate the maps.
 * 
 * @author Tinnon Luong
 */
public class StartSearch {

	/**
	 * The map that Bryan navigates through
	 */
	private static Map desertMap;

	public static void main(String[] args) throws InvalidMapException, FileNotFoundException, IOException {

		// Stores the map txt file
		String mapFileName = args[0];

		if (args.length < 1) { // if no txt file name is given
			System.out.println("You must provide the name of the input file");
			System.exit(0);
		}

		StartSearch newAdventure = new StartSearch(mapFileName);
		newAdventure.adventureAlgorithm();

	}

	/**
	 * Constructor creates the map
	 * 
	 * @param filename name of the map
	 * @throws InvalidMapException
	 * @throws FileNotFoundException
	 * @throws IOException
	 */
	public StartSearch(String filename) throws InvalidMapException, FileNotFoundException, IOException {
		this.desertMap = new Map(filename);

	}

	/**
	 * Finds the next best cell for Bryan to move into
	 * 
	 * @param cell cell that Bryan is currently in
	 * @return index of the next best cell for Bryan to move into
	 */
public MapCell bestCell(MapCell cell) {

		// Get the four neighbouring cells of the current cell that Bryan is in
		MapCell zero = cell.getNeighbour(0);
		MapCell one = cell.getNeighbour(1);
		MapCell two = cell.getNeighbour(2);
		MapCell three = cell.getNeighbour(3);

		/*
		 * Add neighbour cell !=null everywhere
		 * 
		 * Note that if cell is a horizontal path, it can never access indexes 0 and 2.
		 * Likewise, if cell is a vertical path, it can never access indexes 1 and 3.
		 * So we have to add !cell.isHorizontalPath() and !cell.isVerticalPath() where applicable.
		 */
		if ((zero != null && !zero.isMarked() && zero.isGem() && !cell.isHorizontalPath())
				|| (one != null && !one.isMarked() && one.isGem() && !cell.isVerticalPath())
				|| (two != null && !two.isMarked() && two.isGem() && !cell.isHorizontalPath())
				|| (three != null && !three.isMarked() && three.isGem() && !cell.isVerticalPath())) {

			if (zero != null && !zero.isMarked() && zero.isGem() && !cell.isHorizontalPath()) {

				return zero;

			}
			if (one != null && !one.isMarked() && one.isGem() && !cell.isVerticalPath()) {

				return one;

			}
			if (two != null && !two.isMarked() && two.isGem() && !cell.isHorizontalPath()) {

				return two;

			}
			if (three != null && !three.isMarked() && three.isGem() && !cell.isVerticalPath()) {

				return three;

			}

		} else if ((zero != null && !zero.isMarked() && zero.isCrossPath() && !cell.isHorizontalPath())
				|| (one != null && !one.isMarked() && one.isCrossPath() && !cell.isVerticalPath())
				|| (two != null && !two.isMarked() && two.isCrossPath() && !cell.isHorizontalPath())
				|| (three != null && !three.isMarked() && three.isCrossPath() && !cell.isVerticalPath())) {
			if (zero != null && !zero.isMarked() && zero.isCrossPath() && !cell.isHorizontalPath()) {

				return zero;

			}
			if (one != null && !one.isMarked() && one.isCrossPath() && !cell.isVerticalPath()) {

				return one;

			}
			if (two != null && !two.isMarked() && two.isCrossPath() && !cell.isHorizontalPath()) {

				return two;

			}
			if (three != null && !three.isMarked() && three.isCrossPath() && !cell.isVerticalPath()) {

				return three;

			}

		}
		
		// Neighbouring cells at indexes 1 and 3 (right and left of cell) 
		// which are vertical paths can never be returned
		else if ((zero != null && !zero.isMarked() && zero.isVerticalPath() && !cell.isHorizontalPath())
				|| (two != null && !two.isMarked() && two.isVerticalPath()) && !cell.isHorizontalPath()) {
			if (zero != null && !zero.isMarked() && zero.isVerticalPath() && !cell.isHorizontalPath()) {

				return zero;

			}

			if (two != null && !two.isMarked() && two.isVerticalPath() && !cell.isHorizontalPath()) {

				return two;

			}

		} 
		// Likewise, neighbouring cells at indexes 0 and 2 (above and below cell) 
		// which are horizontal paths can never be returned
		else if ((one != null && !one.isMarked() && one.isHorizontalPath() && !cell.isVerticalPath())
				|| (three != null && !three.isMarked() && three.isHorizontalPath() && !cell.isVerticalPath())) {

			if (one != null && !one.isMarked() && one.isHorizontalPath() && !cell.isVerticalPath()) {

				return one;

			}
			if (three != null && !three.isMarked() && three.isHorizontalPath() && !cell.isVerticalPath()) {

				return three;

			}
		}
		return null;
	}

	/**
	 * This method is the algorithm that Bryan follows to navigate through the map
	 */
	public void adventureAlgorithm() {

		/**
		 * number of gems that Bryan finds in the map
		 */
		int gemsFound;
		/**
		 * number of gems that Bryan places in his bag
		 */
		int gemsCollected;

		/**
		 * an empty stack represents the path that Bryan takes
		 */
		ArrayStack<MapCell> adventurePath = new ArrayStack<MapCell>();

		/**
		 * cell that Bryan starts in
		 */
		MapCell startCell = desertMap.getStart();

		/**
		 * number of gems that Bryan can fit in his bag
		 */
		int bagSize = desertMap.bagSize();

		/**
		 * cell that Bryan is currently in
		 */
		MapCell currentCell;

		/**
		 * next best cell for Bryan to move into
		 */
		MapCell nextBestCell;

		/**
		 * cell that Bryan cannot or will not move into
		 */
		MapCell deadEnd;

		// At the start of his adventure, Bryan has neither found nor collected any
		// gems.
		gemsFound = 0;
		gemsCollected = 0;

		// Push the start cell to the stack and mark it as Initial.
		adventurePath.push(startCell);
		startCell.markInitial();

		// While the stack is not empty
		while (!adventurePath.isEmpty()) {

			// Find the cell that Bryan is currently in.
			currentCell = adventurePath.peek();

			// Find the next best cell for Bryan to move into.
			nextBestCell = bestCell(currentCell);

			// If a cell is available for Bryan to move into,
			if (nextBestCell != null) {

				// push that cell to the stack (Bryan moves into that cell) and mark as inStack.
				adventurePath.push(nextBestCell);
				nextBestCell.markInStack();

				// If that cell is a gem cell,
				if (nextBestCell.isGem()) {

					// The number of gems that Bryan has found increases by one;
					gemsFound++;

					// if Bryan's bag is not yet full,
					if (gemsFound <= bagSize) {
						// the number of gems that Bryan has collected in his bag increases by one
						gemsCollected++;
					}

				}
			}

			// If there are no cells available for Bryan to move into,
			else {
				// pop the current cell that Bryan is in (Bryan takes a step back) and mark as
				// outStack.
				deadEnd = adventurePath.pop();
				deadEnd.markOutStack();

			}

		}

		// Print out the number of gems that Bryan has placed in his bag.
		System.out.println("Bryan collected " + gemsCollected + " gem(s) in his bag!");
	}
}  
  
  
  
  
  
